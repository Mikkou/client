<script>
import loadjs from 'loadjs'
import errorMixin from '../mixins/error'

export default {
    name: 'AwTel',

    inheritAttrs: false,

    mixins: [errorMixin],

    data() {
        return {
            VueTelInput: null
        }
    },

    mounted() {
        this.loadTelInput().then(() => {
            this.VueTelInput = window.VueTelInput.VueTelInput
            this.$nextTick(this.bindErrorListener)
        })
    },

    beforeDestroy() {
        if (this.VueTelInput) {
            this.bindErrorListener(false)
        }
    },

    render(h) {
        return this.VueTelInput
            ? h(
                  'div',
                  {
                      staticClass: 'aw-text-field is-tel',
                      class: { 'has-error': this.hasError }
                  },
                  [
                      h(this.VueTelInput, {
                          props: {
                              ...this.$attrs,
                              placeholder:
                                  this.$attrs.label || this.$attrs.placeholder,
                              inputClasses: 'aw-text-field__element p-3'
                          },
                          on: {
                              ...this.$listeners,
                              input: this._onInputProxy
                          }
                      }),
                      this.errorText
                          ? h('span', {
                                staticClass:
                                    'aw-error is-center has-pin-bottom-center',
                                domProps: {
                                    innerText: this.errorText
                                },
                                on: { click: this._onErrorClick }
                            })
                          : null
                  ]
              )
            : h('AwInput', { attrs: { type: 'tel', ...this.$attrs } })
    },

    methods: {
        loadTelInput() {
            if (window.VueTelInput) {
                return Promise.resolve()
            }

            return loadjs(
                ['https://unpkg.com/vue-tel-input'],
                'vue-tel-input',
                {
                    returnPromise: true
                }
            )
        },

        bindErrorListener(attach = true) {
            const method = attach ? 'addEventListener' : 'removeEventListener'
            const input = this._getElement()
            input[method]('invalid', this._onInvalid)
        },

        // overwrite element getter from error mixin
        _getElement() {
            return this.$el && this.$el.querySelector('input[type="tel"]')
        },

        _onInputProxy(text) {
            if (this.hasError && !!text) {
                this.setError('')
            }
            this.$emit('input', ...arguments)
        }
    }
}
</script>
