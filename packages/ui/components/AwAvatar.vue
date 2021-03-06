<template functional>
    <span
        :style="[
            data.staticStyle,
            data.style,
            {
                'background-color': props.isColored
                    ? $options.getColor(props.name)
                    : '',
                width: `${props.size}px`,
                height: `${props.size}px`
            }
        ]"
        :class="[
            data.staticClass,
            data.class,
            props.isColored ? '' : 'bg-disabled'
        ]"
        class="inline-block align-middle rounded-full relative text-center text-surface"
    >
        <slot v-bind="{ ...props, initials: $options.getColor(props.name) }">
            <img
                v-if="$options.getSrc(props.src)"
                :height="props.size"
                :src="props.src"
                :alt="props.name || props.src"
                class="rounded-full"
            />
        </slot>
        <span
            v-if="!$options.getSrc(props.src) && props.type === 'initials'"
            class="absolute top-1/2 left-1/2 uppercase"
            style="transform: translate(-50%, -50%);"
            >{{ $options.getLetters(props.name) }}</span
        >
        <slot
            name="no-img"
            v-bind="{ ...props, initials: $options.getColor(props.name) }"
        >
            <AwIcon
                v-if="!$options.getSrc(props.src) && props.type === 'no-img'"
                class="absolute text-surface top-1/2 left-1/2 text-xs"
                style="transform: translate(-50%, -50%);"
                name="user"
            />
        </slot>
    </span>
</template>

<script>
import AwIcon from './AwIcon.vue'

export default {
    name: 'AwAvatar',

    components: {
        AwIcon
    },

    props: {
        // Full URL to the picture
        src: {
            type: String,
            default: ''
        },
        // User name
        name: {
            type: String,
            required: true
        },
        // Size of the image
        size: {
            type: Number,
            default: 36
        },
        // Type of the rendered image.
        type: {
            type: String,
            // initials , empty, no-img
            default: 'initials',
            validator(value) {
                return ['initials', 'empty', 'no-img'].includes(value)
            }
        },
        // To bring the color to the rounded background.
        isColored: {
            type: Boolean,
            default: true
        }
    },

    // To get source of the image if exist
    getSrc(src) {
        return src
    },
    // Convert user name to 2 first letters
    getLetters(name) {
        const parts = name.split(/[ -]/)
        let initials = ''
        for (let i = 0; i < parts.length; i++) {
            initials += parts[i].charAt(0)
        }
        if (initials.length > 3 && initials.search(/[A-Z]/) !== -1) {
            initials = initials.replace(/[a-z]+/g, '')
        }
        initials = initials.substr(0, 2)
        return initials
    },
    // Get randome color from the preset list
    getColor(name) {
        const colors = [
            '#FF5722',
            '#FFA726',
            '#FFCA28',
            '#CDDC39',
            '#8BC34A',
            '#4CAF50',
            '#009688',
            '#00ACC1',
            '#03A9F4',
            '#2196F3',
            '#3F51B5',
            '#673AB7',
            '#9C27B0',
            '#EC407A',
            '#F44336'
        ]
        return colors[name.length % colors.length]
    }
}
</script>
