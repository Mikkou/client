<template functional>
    <thead v-if="props.columns.length">
        <tr class="bg-muted-dark border-b">
            <th
                v-for="(column, key) in props.columns"
                :key="key"
                class="py-3 px-4 lg:py-5 lg:px-6 text-left font-normal"
                :class="column.align"
            >
                <slot name="th" v-bind="{ column, key }">
                    {{ column.text }}
                </slot>
            </th>
        </tr>
    </thead>
</template>

<script>
import { validateBySchema } from '../assets/js/component'

export default {
    name: 'AwTableHead',

    props: {
        columns: {
            type: Array,
            default: () => [],
            validator: validateBySchema([
                { text: 'string', 'align?': 'string' }
            ])
        }
    }
}
</script>
