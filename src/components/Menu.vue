<template>
  <input type="text" v-model="q" v-if="!submenu" />

  <div :class="{ menu: true, submenu: submenu }" :style="style">
    <transition name="component-fade" appear mode="out-in">
      <ul>
        <li
          v-for="(item, index) in filteredMenu"
          :key="item.id"
          @click="setHoveredIndex($event, index)"
          @mouseenter="setHoveredIndex($event, index)"
        >
          <img v-lazy="item.thumbnail" />
          <a :href="`${rootUrl}/${item.slug}`">
            {{ item.name }}
          </a>
          <span
            v-if="item.submenu"
            :class="{
              'material-icons': true,
              active: index === hoveredIndex,
            }"
          >
            keyboard_arrow_down
          </span>
        </li>
      </ul>
    </transition>

    <Menu
      v-if="haveSubmenu"
      :style="submenuClass"
      :menuItems="hoveredMenu.submenu"
      :submenu="true"
      :key="hoveredMenu.id"
      :rootUrl="path"
    />
  </div>
</template>

<script>
export default {
  name: "Menu",
  data() {
    return {
      info: "",
      showChildren: false,
      hoveredIndex: -1,
      q: "",
      show: false,
    };
  },
  props: {
    menuItems: Array,
    className: { type: String, default: "" },
    submenu: { type: Boolean, default: false },
    rootUrl: { type: String, default: "/catalog" },
    style: { type: Object, default: () => ({}) },
  },

  computed: {
    hoveredMenu() {
      return this.filteredMenu[this.hoveredIndex] || { submenu: [] };
    },
    haveSubmenu() {
      if (!this.hoveredMenu || !this.hoveredMenu.submenu) {
        return false;
      }

      return this.hoveredMenu.submenu.length;
    },
    filteredMenu() {
      if (!this.q) return this.menuItems;
      const filteredItems = this.menuItems.reduce(this._recursiveFilterFn, []);
      return filteredItems;
    },
    path() {
      return `${this.rootUrl}/${this.hoveredMenu.slug}`;
    },
    submenuClass() {
      return {
        "margin-top": `${this.hoveredIndex * 40}px`,
      };
    },
  },
  watch: {
    menuItems: function () {
      this.hoveredIndex = -1;
    },
  },

  methods: {
    _recursiveFilterFn(acc, menuItem) {
      const submenu = menuItem.submenu
        ? menuItem.submenu.reduce(this._recursiveFilterFn, [])
        : undefined;

      if (
        menuItem.name.toLowerCase().includes(this.q.toLowerCase()) ||
        (Array.isArray(submenu) && submenu.length > 0)
      ) {
        acc.push({ ...menuItem, submenu });
      }
      return acc;
    },
    setHoveredIndex(e, index) {
      this.hoveredIndex = index;
    },
  },
};
</script>

<style>
@media screen and (max-width: 400px) {
  .menu {
    position: absolute;
    width: 100vw;
  }
  .submenu {
    position: relative;
    margin-left: -10px;
  }
}
img {
  width: 36px;
  height: 36px;
}

ul {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  list-style-type: none;
  padding-left: 10px;
}

li {
  min-height: 40px;
  background-color: white;
  align-items: center;
  border: 1px solid black;
  cursor: pointer;
  display: flex;
  margin: 1px;
  width: 100%;
  justify-content: flex-start;
}

.menu {
  display: flex;
  flex-direction: row;
}


.material-icons {
  transition: transform 0.2s ease-in-out;
  margin-left: auto;
}

.active {
  transform: rotateZ(-90deg);
}

.component-fade-enter-active,
.component-fade-leave-active {
  transition: opacity 0.3s ease;
}

.component-fade-enter-from,
.component-fade-leave-to {
  opacity: 0;
}
</style>
