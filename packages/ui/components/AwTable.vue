<template>
    <table
        class="w-full text-sm leading-snug overflow-hidden max-w-full shadow-md"
    >
        <!-- Table head, passes `thead` object to the scope -->
        <slot name="thead" :thead="theadVisible">
            <!-- `AwTableHead` component -->
            <AwTableHead :columns="theadVisible" />
        </slot>

        <!-- body -->
        <tbody @click="handleRowClick">
            <!-- Table row component, recieves `rows` prop -->
            <slot name="tr" :rows="rows">
                <template v-for="(rowData, rowIndex) in rows">
                    <AwTableRow
                        :key="rowData.id || rowIndex"
                        v-bind="{
                            rowData,
                            rowIndex,
                            active: openedRow === rowIndex,
                            hasRowClickListener,
                            ...groupedColumns
                        }"
                    />
                </template>
            </slot>
        </tbody>

        <!-- footer -->
        <slot name="tfoot">
            <!-- <AwTableFoot /> -->
        </slot>
    </table>
</template>

<script>
import { keys, path, pathOr } from 'rambdax'
import { ucFirst, toPascal } from '../assets/js/string'
import {
    TABLE_PRIORITY_ATTR,
    TABLE_TOGGLER_ATTR,
    TABLE_INDEX_ATTR,
    TABLE_ROW_CLICK_EVENT
} from '../assets/js/constants'
import AwTableHead from './AwTableHead.vue'
import AwTableCol from './AwTableCol.vue'
import AwTableRow from './AwTableRow.vue'

const WIDTH_THRESHOLD = 50 // px
const RESIZE_DEBOUNCE = 500 // ms

export default {
    name: 'AwTable',

    components: {
        AwTableHead,
        AwTableRow
    },

    props: {
        rows: {
            type: Array,
            required: true
        },

        verticalAlign: {
            type: String,
            validator(value) {
                return ['align-bottom', 'align-middle', 'align-top'].includes(
                    `align-${value}`
                )
            },
            default: 'middle'
        }
    },

    data() {
        return {
            hiddenColsIndexes: [],
            openedRow: null
        }
    },

    computed: {
        columns() {
            const _slots = this.$slots.default

            if (Array.isArray(_slots)) {
                return _slots
                    .filter(this._isColumnComponent)
                    .map(({ componentOptions, data }, i) => {
                        const props = componentOptions.propsData
                        const slot = path('scopedSlots.default', data)
                        const field = path('field', props)
                        const title = pathOr(
                            field ? ucFirst(field) : '',
                            'title',
                            props
                        )

                        return {
                            field,
                            verticalAlign: pathOr(
                                this.verticalAlign,
                                'verticalAlign',
                                props
                            ),
                            priority: pathOr(i, 'priority', props),
                            title,
                            textAlign: path('textAlign', props),
                            slot
                        }
                    })
            } else {
                return keys(this.rows[0]).map((field, priority) => ({
                    field,
                    title: ucFirst(field),
                    priority,
                    verticalAlign: this.verticalAlign
                }))
            }
        },

        groupedColumns() {
            const columns = this.columns
            const visibleColumns = []
            const hiddenColumns = []

            for (let i = 0, max = columns.length; i < max; i++) {
                if (this.hiddenColsIndexes.includes(i)) {
                    hiddenColumns.push(columns[i])
                } else {
                    visibleColumns.push(columns[i])
                }
            }

            return { visibleColumns, hiddenColumns }
        },

        theadVisible() {
            const { visibleColumns, hiddenColumns } = this.groupedColumns

            return visibleColumns
                .map(({ title, textAlign }) => ({
                    text: title,
                    align: textAlign && `text-${textAlign}`
                }))
                .concat(hiddenColumns.length ? [{ text: '' }] : [])
        },

        hasRowClickListener() {
            return TABLE_ROW_CLICK_EVENT in this.$listeners
        }
    },

    watch: {
        rows() {
            this.openedRow = null
        }
    },

    mounted() {
        if (!this.$scopedSlots.tr) {
            this.setResizeListener()
        }
    },

    methods: {
        toggleRow(index) {
            this.openedRow = this.openedRow === index ? null : index
        },

        setResizeListener() {
            let resizeTm

            const onResize = () => {
                clearTimeout(resizeTm)
                resizeTm = setTimeout(async () => {
                    // reset indexes if needed and await DOM update
                    if (this.hiddenColsIndexes.length) {
                        this.hiddenColsIndexes = []
                        await this.$nextTick()
                    }

                    // get calculated columns
                    const newIndexes = this._getHiddenColsIndexes()

                    if (!newIndexes.length) {
                        this.openedRow = null
                    }
                    this.hiddenColsIndexes = newIndexes
                }, RESIZE_DEBOUNCE)
            }

            // first time calculations
            this.$nextTick(() => {
                this.hiddenColsIndexes = this._getHiddenColsIndexes()
            })

            window.addEventListener('resize', onResize)

            this.$once('hook:beforeDestroy', () => {
                clearTimeout(resizeTm)
                window.removeEventListener('resize', onResize)
            })
        },

        handleRowClick($event) {
            const target = $event.target
            const toggler = target.hasAttribute(TABLE_TOGGLER_ATTR)
                ? target
                : target.closest(`[${TABLE_TOGGLER_ATTR}]`)

            // hidden row toggler click
            if (toggler) {
                const index = parseInt(toggler.getAttribute(TABLE_TOGGLER_ATTR))
                this.toggleRow(index)
                return
            }

            // if table has no row click listener exit
            if (!this.hasRowClickListener) return

            const row = target.closest(`[${TABLE_INDEX_ATTR}]`)

            if (row) {
                const index = parseInt(row.getAttribute(TABLE_INDEX_ATTR))
                const data = this.rows[index]
                this.$emit(TABLE_ROW_CLICK_EVENT, data, index, $event)
            }
        },

        // Filters only AwTableCol components from scoped slot
        _isColumnComponent(vNode) {
            const tagName = path('componentOptions.tag', vNode)
            const tagNamePascal = toPascal(tagName)

            return tagNamePascal === AwTableCol.name
        },

        _getParentEl() {
            return pathOr(null, '$el.parentElement', this)
        },

        _getFirstRow() {
            return Array.from(
                this.$el.querySelectorAll(
                    `tbody > :first-child > [${TABLE_PRIORITY_ATTR}]`
                )
            )
        },

        _hasOverflowWidth() {
            const parent = this._getParentEl()

            if (parent) {
                return parent.clientWidth < this.$el.scrollWidth
            } else {
                return false
            }
        },

        _sortColumnsByPriority(columns) {
            return columns
                .map((el, index) => ({ el, index }))
                .sort((a, b) => {
                    // parseFloat accepts Infinity value
                    const aPrior = parseFloat(
                        a.el.getAttribute(TABLE_PRIORITY_ATTR)
                    )
                    const bPrior = parseFloat(
                        b.el.getAttribute(TABLE_PRIORITY_ATTR)
                    )

                    return aPrior - bPrior
                })
        },

        _getHiddenColsIndexes() {
            // manipulate only first row
            const columns = this._getFirstRow()

            let hiddenIndexes = []

            if (this._hasOverflowWidth()) {
                // calculate columns
                const sorted = this._sortColumnsByPriority(columns)

                let maxWidth = this._getParentEl().clientWidth

                hiddenIndexes = sorted.reduce((acc, { el, index }) => {
                    // subtract el width from global width
                    maxWidth -= el.offsetWidth

                    return maxWidth < WIDTH_THRESHOLD ? acc.concat(index) : acc
                }, [])
            }

            return hiddenIndexes
        }
    }
}
</script>
