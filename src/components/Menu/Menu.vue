<template>
  <div ref="container" class="kro-menu">
    <div class="kro-menu__scrim" :class="{ 'kro-menu__scrim--is-open': isOpen }" @click="close" />
    <slot :open="open" name="activator" />

    <div ref="menu" class="kro-menu__content" :class="{ ...classes, 'kro-menu__content--is-open': isOpen }">
      <slot />
    </div>
  </div>
</template>

<script lang="ts">
import { ref, reactive } from 'vue'
import { usePositioning } from './composables/menu'

export default {
  name: 'KroMenu',
  props: {
    offsetX: {
      type: Boolean,
      default: false,
    },
    offsetY: {
      type: Boolean,
      default: false,
    },

    left: Boolean,
    right: Boolean,
    top: Boolean,
    bottom: Boolean,

  },
  emits: ['open', 'close'],
  setup(props: any, { emit }: { emit: any }) {
    const isOpen = ref(false)

    const container = ref<HTMLElement | null>(null)
    const menu = ref<HTMLElement | null>(null)

    const classes = reactive<{ [name: string]: boolean }>({
      'kro-menu__content--y-bottom': false,
      'kro-menu__content--y-bottom-offset': false,

      'kro-menu__content--y-top': false,
      'kro-menu__content--y-top-offset': false,

      'kro-menu__content--x-left': false,
      'kro-menu__content--x-left-offset': false,

      'kro-menu__content--x-right': false,
      'kro-menu__content--x-right-offset': false,
    })

    const open = () => {
      // Reset all classes on object.
      Object.keys(classes).forEach((key: string) => classes[key] = false)

      const { offsetX, offsetY, left, right, top, bottom } = props
      const { canFit } = usePositioning(container, menu, { offsetX, offsetY })

      /**
                 * Handle the vertical positioning of the menu.
                 */
      if (canFit.top && top) {
        classes[`kro-menu__content--y-top${offsetY ? '-offset' : ''}`] = true
      }
      else if (canFit.bottom && bottom) {
        classes[`kro-menu__content--y-bottom${offsetY ? '-offset' : ''}`] = true
      }
      else if (canFit.bottom) {
        classes[`kro-menu__content--y-bottom${offsetY ? '-offset' : ''}`] = true
      }
      else if (canFit.top) {
        classes[`kro-menu__content--y-top${offsetY ? '-offset' : ''}`] = true
      }
      else {
      }

      /**
                 * Handle the horizontal positioning of the menu.
                 */
      if (canFit.left && left) {
        classes[`kro-menu__content--x-left${offsetX ? '-offset' : ''}`] = true
      }
      else if (canFit.right && right) {
        classes[`kro-menu__content--x-right${offsetX ? '-offset' : ''}`] = true
      }
      else if (canFit.right) {
        classes[`kro-menu__content--x-right${offsetX ? '-offset' : ''}`] = true
      }
      else if (canFit.left) {
        classes[`kro-menu__content--x-left${offsetX ? '-offset' : ''}`] = true
      }
      else {

      }

      emit('open')
      isOpen.value = true
    }

    const close = () => {
      emit('close')
      isOpen.value = false
    }

    const toggle = () => { isOpen.value ? close() : open() }

    return {
      // Props
      isOpen,

      classes,

      // Events
      open,
      close,
      toggle,

      // Refs
      menu,
      container,
    }
  },
}
</script>

<style lang="scss">

    @import '../../styles/general/layers';

    .kro-menu {
        display: inline-block;
        position: relative;

        --kro-menu-offset-x: 0;
        --kro-menu-offset-y: 0;
    }

        .kro-menu__scrim {
            @include useLayer(menu);
            @apply fixed inset-0 pointer-events-none;

            &.kro-menu__scrim--is-open {
                pointer-events: all;
            }
        }

        .kro-menu__content {
            @include useLayer(menu);

            @apply block pointer-events-none absolute rounded-md overflow-hidden;
            @apply overflow-hidden opacity-0;

            min-width: var(--kro-menu-min-with, 200px);
            max-width: var(--kro-menu-max-width, 300px);
            width: var(--kro-menu-width);

            background: var(--kro-background-secondary);
            box-shadow: var(--kro-shadow);

            transform: scale(0);
            transition: transform 150ms cubic-bezier(0.4, 0.0, 0.2, 1), opacity 150ms cubic-bezier(0.4, 0.0, 0.2, 1);
            transform-origin: var(--kro-menu-transform-origin-x) var(--kro-menu-transform-origin-y);

            &.kro-menu__content--is-open {
                opacity: 1;
                transform: scale(1);
                pointer-events: all;
            }

            &.kro-menu__content--y-bottom { top: 0; --kro-menu-transform-origin-y: top; }
            &.kro-menu__content--y-bottom-offset { top: 100%; --kro-menu-transform-origin-y: top; }

            &.kro-menu__content--y-top { bottom: 0; --kro-menu-transform-origin-y: bottom; }
            &.kro-menu__content--y-top-offset { bottom: 100%; --kro-menu-transform-origin-y: bottom; }

            &.kro-menu__content--x-left { right: 0; --kro-menu-transform-origin-x: right; }
            &.kro-menu__content--x-left-offset { right: 100%; --kro-menu-transform-origin-x: right; }

            &.kro-menu__content--x-right { left: 0; --kro-menu-transform-origin-x: left; }
            &.kro-menu__content--x-right-offset { left: 100%; --kro-menu-transform-origin-x: left; }

        }

</style>
