<template>
  <div ref="container"></div>
</template>

<script>
  import recaptcha from './recaptcha.js';

  let widgetId = null;

  export default {
    props: {
      dataKey: {
        type: String,
        required: true
      },
      options: {
        type: Object,
        default() {
          return {};
        }
      }
    },
    created() {
      recaptcha.checkRecaptchaLoad();
    },
    mounted() {
      const self = this;
      const opts = Object.assign({}, this.options, {
        callback: this.emitVerify,
        'expired-callback': this.emitExpired
      });
      recaptcha.render(this.$refs.container, this.dataKey, opts)
        .then((id) => {
          widgetId = id;
          self.$emit('render', widgetId);
        });
    },
    methods: {
      reset() {
        recaptcha.reset(widgetId);
      },
      emitVerify(response) {
        this.$emit('verify', response);
      },
      emitExpired() {
        this.$emit('expired');
      }
    },
    events: {
      recaptchaReset() {
        this.reset();
      }
    }
  };
</script>
