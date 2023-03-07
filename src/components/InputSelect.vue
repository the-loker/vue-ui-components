<script>
  export default {
    props: {
      modelValue: {
        type: [ String, Array ]
      },
      items: {
        type: Array,
        default: []
      },
      multiple: {
        type: Boolean,
        default: false
      },
      filterable: {
        type: Boolean,
        default: false
      },
      disabled: {
        type: Boolean,
        default: false
      },
      placeholder: {
        type: String,
        default: 'Выбрать...'
      },
      filterable_placeholder: {
        type: String,
        default: 'Найти...'
      }
    },

    data() {
      return {
        selected: this.multiple ? [] : '',
        listItems: this.items,
        filter: '',
        isOpenList: false,
      }
    },

    methods: {
      selectedItem(value) {
        if(Array.isArray(this.selected)) {
          this.selected.push(value);
        }

        if(typeof this.selected === 'string') {
          this.selected = value;
        }
      },

      unSelectedItem(value) {
        if(Array.isArray(this.selected)) {
          this.selected.splice(this.selected.indexOf(value), 1);
        }

        if(typeof this.selected === 'string') {
          this.selected = '';
        }
      },

      select(e) {
        const elem = e.target;
        const { value } = elem.attributes['data-value'];
        const isSelected = this.isSelected(value);

        if(isSelected) {
          this.unSelectedItem(value);
        } else {
          this.selectedItem(value);
        }

        this.$emit('update:modelValue', this.selected);
      },

      listOptionsHandler(e) {
        const el = e.target;

        if(!el.closest(".js-input-select")) {
          this.isOpenList = false;

          return;
        }

        this.isOpenList = true;
      },

      listFilter(e) {
        const val = e.target.value;

        this.listItems = this.items.filter(item => {
          const option = item.option.toLowerCase();

          if(option.includes(val.toLowerCase())) return true;

          return false;
        });
      },

      remove(value) {
        this.unSelectedItem(value);
      },

      isSelected(val) {
        if(typeof this.selected === 'string') {
          return this.selected == val;
        }

        if(Array.isArray(this.selected)) {
          return this.selected.some(item => item == val);
        }
      }
    },

    emits: {
      'update:modelValue': null
    },

    mounted() {
      document.addEventListener("click", this.listOptionsHandler);
    },
    unmounted() {
      document.removeEventListener("click", this.listOptionsHandler);
    }
  }
</script>

<template>
  <div class="input-select js-input-select">
    <input type="hidden" v-model="selected">
    <div class="input-select--selected">
      <div
        class="input-select--placeholder"
        :style="{ display: modelValue.length ? 'none' : '' }"
      >{{ placeholder }}</div>
      <template
        v-for="item, idx in items"
        :key="idx"
      >
        <div
          v-if="isSelected(item.id)"
          class="selected-value"
        >
          <div class="selected-value--text">{{ item.option }}</div>
          <div class="selected-value--btn-container">
            <button
              @click.stop="remove(item.id)"
              class="selected-value-button"
              type="button"
            >&times;</button>
          </div>
        </div>
      </template>
    </div>
    <div class="input-select-options">
      <div :class="{ show: isOpenList }" class="input-select--list">
        <div class="input-select--list-search">
          <input
            v-if="filterable"
            @input="listFilter"
            type="text"
            :placeholder="filterable_placeholder"
          >
        </div>
        <div class="input-select--list-container">
          <ul @click.stop="select" class="input-select--list-container--items">
            <li
              v-for="item, idx in listItems"
              :key="idx"
              :data-value="item.id"
              :class="{ selected: isSelected(item.id) }"
            >{{ item.option }}</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
  .input-select {
    box-sizing: border-box;
    min-width: 200px;
    min-height: 36px;
    position: relative;
    border: 1px solid #dbdedf;
    border-radius: 3px;
    background: #ffffff;
    font-size: 15px;
    color: #313942;
    cursor: pointer;

    &[disabled] {
      opacity: 0.5;
      cursor: default;

      .selected-value-button {
        cursor: default;
      }

      .selected-value-button:hover {
        color: #878787;
      }
    }

    &-options {
      position: relative;
    }

    &--selected {
      display: flex;
      flex-wrap: wrap;
      padding: 8px 9px 7px;
      gap: 5px;
    }

    &--placeholder {
      color: #2e3640;
    }

    &--list {
      display: none;
      width: 100%;
      position: absolute;
      top: 8px;
      left: 0;
      outline: 1px solid #dbdedf;
      border-radius: 3px;
      background-color: #ffffff;
      z-index: 9999;

      &:after {
        display: block;
        content: "";
        position: absolute;
        width: 0;
        top: -5px;
        left: 50%;
        margin-left: -10px;
        border-style: solid;
        border-color: #dbdedf transparent;
        border-width: 0 4px 4px;
        z-index: 99999;
      }

      &-search {
        margin: 10px 20px;

        input {
          width: calc(100% - 20px);
          padding: 5px 10px;
          border: none;
          border-radius: 4px;
          outline: 1px solid #dbdedf;;
        }

        .search-not-found {
          padding: 5px 10px;
          color: rgb(228, 31, 31);
        }
      }
    }

    &--list.show {
      display: block;
    }

    &--list-container {
      max-height: 200px;
      overflow: hidden;
      overflow-y: auto;

      &--items {
        margin: 0;
        padding: 0;
        list-style: none;

        & li {
          position: relative;
          padding: 5px 20px;
          user-select: none;

          &:hover {
            background-color: #efefef;
          }
        }
      }

      &__group {
        button {
          width: 100%;
          border: none;
          background: none;
          padding: 10px;
          text-align: left;
          font-size: 15px;
          cursor: pointer;

          &:hover {
            background-color: #dbdedf;
          }
        }

        ul {
          position: relative;
          margin: 0;
          padding: 0;
          list-style: none;

          li {
            padding: 5px 20px;

            &:hover {
              background-color: #efefef;
            }
          }
        }
      }
    }

    .selected-value {
      display: flex;
      flex-wrap: nowrap;
      align-items: center;
      flex: 1 1 auto;
      padding: 2px 4px 2px 8px;
      border: 1px solid #dbdedf;
      border-radius: 4px;
      overflow: hidden;

      &--text {
        width: 100%;
        text-overflow: ellipsis;
        white-space: nowrap;
        overflow: hidden;

        &:hover {
          white-space: normal;
          overflow: auto;
        }
      }

      &--btn-container {
        display: flex;
        margin-left: 5px;
      }

      &-button {
        border: none;
        background: none;
        padding: 0;
        line-height: 0;
        font-size: 25px;
        color: #878787;
        cursor: pointer;

        &:hover {
          color: #343434;
        }
      }
    }

    .selected::before {
      content: "";
      position: absolute;
      left: 6px;
      width: 7px;
      height: 7px;
      margin-top: 6px;
      border-radius: 50%;
      background-color: #00ac06;
    }
  }

</style>