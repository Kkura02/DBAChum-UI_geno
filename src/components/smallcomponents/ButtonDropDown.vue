<template>
    <div>
        <button @click="toggleMenu" class="rounded create-or-add ml-2 h-10 w-48 text-white text-xs font-bold shadow-black shadow-md">{{btnText}}</button>
        <ul v-if="showMenu" class="bg-gray-800 w-48 rounded ml-2 mt-2 py-2 flex flex-col justify-evenly text-gray-400">
            <li v-for="item in items" :key="item" class="text-center rounded mx-4 my-1 py-1 hover:bg-gray-700 hover:text-gray-100 cursor-pointer">
                <a @click="selectItem(item)">{{item}}</a>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
  props: {
    btnText: String,
    items: { // Optional prop for dropdown items (default to empty array)
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      showMenu: false,
    };
  },
  methods: {
    toggleMenu() {
      this.showMenu = !this.showMenu;
    },
    selectItem(item) {
      // Emit an event with the selected item
      this.$emit('selectedItem', item);
      // Optionally close the menu
      this.showMenu = false;
    },
  },
};
</script>