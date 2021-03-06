<template>
  <div class="kro-select">
    <div :class="{'kro-select__container': true, 'kro-select--focused': focused, 'kro-select--has-text': $attrs.modelValue }">
      <label :class="{'kro-select__label': true, 'kro-select--focused': focused, 'kro-select--has-text': $attrs.modelValue }" :for="id">{{ label }}</label>
      <div class="kro-select__hidden-label">
        {{ label }}
      </div>
      <span :class="{'kro-select__pseudo-label': true, 'kro-select--focused': focused, 'kro-select--has-text': $attrs.modelValue }">{{ label }}</span>
      <select
        :id="id"
        ref="select"
        class="kro-select__input"
        :disabled="disabled"
        :required="required"
        :readonly="readonly"
        :autofocus="autofocus"
        :name="name"
        :value="$attrs.modelValue"

        @change="$emit('update:modelValue', $event.target.value)"
        @focus="focused = true"
        @blur="focused = false"
      >
        <option selected hidden />
        <slot />
      </select>
      <kro-icon class="kro-select__icon" icon="mdi:chevron-down" />
    </div>
  </div>
</template>

<script lang="ts">
import { ref, onMounted } from 'vue'

export default {
  name: 'KroSelect',
  props: {
    label: { type: String },
    required: { type: Boolean },
    disabled: { type: Boolean },
    readonly: { type: Boolean },
    autofocus: { type: Boolean },
    name: { type: String },
    id: { type: String },
  },
  emits: ['update:modelValue'],

  setup(_, { attrs }) {
    const focused = ref(false)
    const select = ref<HTMLSelectElement | null>(null)

    onMounted(() => {
      if (select.value) {
        Array.from(select.value.options).forEach((option) => {
          if (option.value === attrs.modelValue)
            option.selected = true
        })
      }
    })

    return {
      focused,
      select,
    }
  },
}
</script>

<style lang="scss">

    .kro-select {
        display: inline-block;
        cursor: pointer;
        // padding-right: 2rem;
    }

    .kro-select__icon {
        position: absolute;
        right: 0.5rem;
        pointer-events: none;
    }

    .kro-select__container {
        display: inline-grid;
        position: relative;
        border: 2px solid var(--kro-divider);
        border-radius: 0.25rem;
        height: 3rem;

        display: flex;
        justify-content: center;
        flex-direction: column;

        &.kro-select--focused { border-color: var(--kro-primary); }
    }

    .kro-select__hidden-label {
         font-size: 1rem;
        font-weight: 500;
        height: 0;
        opacity: 0;
        padding: 0 0.25rem;
        margin-left: 0.75rem;
        white-space: nowrap;
        pointer-events: none;
    }

    .kro-select__pseudo-label {
        font-size: 0.75rem;
        font-weight: 500;
        position: absolute;
        padding: 0 0.25rem;
        top: 0;
        height: 2px;
        background: var(--kro-background);
        overflow: hidden;
        color: transparent;
        pointer-events: none;
        margin-left: calc(-0.25rem / 2);
        white-space: nowrap;

        transform: translateX(0.75rem) translateY(-2px) scaleX(0);
        transition: transform 150ms cubic-bezier(0.4, 0.0, 0.2, 1);

        &.kro-select--has-text,
        &.kro-select--focused { transform: translateX(0.75rem) translateY(-2px) scaleX(1); }
    }

    .kro-select__label {
        font-size: 1rem;
        font-weight: 500;
        position: absolute;
        height: 100%;
        padding: 0 0.25rem;
        margin-left: 0.75rem;
        pointer-events: none;
        display: flex;
        align-items: center;
        z-index: 1;
        white-space: nowrap;

        transform-origin: left center;
        transition: transform 150ms cubic-bezier(0.4, 0.0, 0.2, 1);

        &.kro-select--focused { color: var(--kro-primary); }

        &.kro-select--has-text,
        &.kro-select--focused {
            transform: scale(0.75) translateY(calc(-50% - 0.6rem));
        }
    }

    .kro-select__input {
        border: none;
        font-size: 1rem;
        font-weight: 500;
        font-family: inherit;
        padding: 0 1rem;
        display: block;
        width: 100%;
        height: 100%;

        color: var(--kro-foreground);
        background: transparent;
        outline: none;
        cursor: pointer;
        appearance: none;

        padding-right: 6.5rem;

        option {
            outline: none;
            background: var(--kro-background-secondary);
        }
    }

</style>
