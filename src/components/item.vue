<template>
  <div class="item" v-show="isShow">
      <span v-bind:style="{'background-color': hovering ? 'lightgray' : 'white'}">
        <span>{{orderNumber}}. </span>
        <input @change="setChecked" type="checkbox" v-model="item.checked">
        <span class="item-text" v-bind:style="{
        'text-decoration': item.checked ? 'line-through' : 'none'}"
        v-show="!editing" v-on:dblclick="editItem" @mouseover="showShadow" @mouseout="hideShadow">{{item.text}}</span>
        <input v-show="editing" autofocus="true" type="text" v-model="item.text" @keyup.esc="cancelEdit" @keyup.enter="doneEdit">
      </span>
  </div>
</template>

<script>
import { SET_CHECKED, DELETE_ITEM, SET_TEXT } from '../store/const-types';
export default {
  name: "item",
  data() {
      let {...item} = this.initItem;
    return {
      item,
      editing: false,
      hovering: false,
      cachedNumber: 0,
      cachedText: ""
    };
  },
  props: {
    initItem: Object,
    index: Number
  },
  computed: {
    isShow() {
      let level = this.$store.state.level;
      if (level === "checked") {
        return this.item.checked === true;
      } else if (level === "unchecked") {
        return this.item.checked === false;
      } return true;
    },
    orderNumber() {
      let level = this.$store.state.level;
      let id = this.item.id;
      let filterItems = this.$store.getters.activeItems;
      if (level === "checked") {
        filterItems =  filterItems.filter(x => x.checked);
      } else if (level === "unchecked") {
        filterItems =  filterItems.filter(x => !x.checked);
      }
      return filterItems.findIndex(x => x.id === id) + 1;
    }
  },
  methods: {
    setChecked() {
      this.$store.dispatch(SET_CHECKED,this.item);
    },
    editItem() {
      this.cachedText = this.item.text;
      this.editing = true;
    },
    cancelEdit() {
      this.editing = false;
      this.item.text = this.cachedText;
    },
    doneEdit() {
      this.editing = false;
      if (!this.item.text) {
        this.$store.dispatch(DELETE_ITEM,this.item);
      }
      else {
        this.$store.dispatch(SET_TEXT,this.item);
        }
    },
    showShadow() {
      this.hovering = true;
    },
    hideShadow() {
      this.hovering = false;
    }
  }
};
</script>

<style scoped>
.item-text {
    margin-left: 30px;
}
</style>
