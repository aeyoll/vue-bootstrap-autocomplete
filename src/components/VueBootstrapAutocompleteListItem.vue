<template>
  <li
    @keydown.tab="$emit('listItemBlur')"
    @keydown.esc.stop.prevent="$emit('listItemBlur')"
    @keydown.down.prevent
    @keydown.up.prevent
    @keyup.enter="$emit('hitActiveListItem', $event)"
    @keyup.down="$emit('selectNextListItem', $event)"
    @keyup.up="$emit('selectPreviousListItem', $event)"
    @blur="processFocusOut"
    tabindex="0"
    href="#"
    :class="textClasses"
  >
    <div class="visually-hidden">{{screenReaderText}}</div>
    <div aria-hidden="true">
      <slot name="suggestion" v-bind="{ data: data, htmlText: htmlText }">
        <span v-html="htmlText"></span>
      </slot>
    </div>
  </li>
</template>

<script>
export default {
  name: 'VueBootstrapAutocompleteListItem',

  props: {
    active: {
      type: Boolean
    },
    data: {},
    screenReaderText: {
      type: String
    },
    htmlText: {
      type: String
    },
    disabled: {
      type: Boolean
    },
    backgroundVariant: {
      type: String
    },
    backgroundVariantResolver: {
      type: Function,
      default: (d) => null,
      validator: d => d instanceof Function
    },
    textVariant: {
      type: String
    }
  },
  data: function() {
    return {
      baseTextClasses: ['vbst-item', 'list-group-item', 'list-group-item-action']
    }
  },

  computed: {
    textClasses() {
      const classes = [...this.baseTextClasses]
      const backgroundVariantResolverResult = this.backgroundVariantResolver(this.data)
      const backgroundVariant =
          (typeof backgroundVariantResolverResult === 'string' && backgroundVariantResolverResult.trim()) ||
          this.backgroundVariant
      if (backgroundVariant) classes.push(`list-group-item-${backgroundVariant}`)
      if (this.textVariant) classes.push(`text-${this.textVariant}`)
      if (this.disabled) classes.push('disabled')
      return classes.join(' ')
    }
  },

  methods: {
    processFocusOut(evt) {
      const tgt = evt.relatedTarget
      if (tgt && tgt.classList.contains('vbst-item')) {
        return
      }

      this.$emit('listItemBlur')
    }
  }
}
</script>

<style scoped>
  li:not(.disabled) {
    cursor: pointer;
  }
  li.disabled {
    cursor: not-allowed;
    pointer-events: none;
  }
</style>
