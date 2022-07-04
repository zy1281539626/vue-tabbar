<template>
  <div class="tab-nav-wrap">
    <div :class="['nav-prev', scrollable.prev ? '' : 'is-disabled']" v-if="scrollable" @click="scrollPrev">
      &lt;
    </div>
    <div :class="['nav-next', scrollable.next ? '' : 'is-disabled']" v-if="scrollable" @click="scrollNext">
      &gt;
    </div>
    <div class="tabs-nav-scroll" ref="navScroll">
      <ul class="nav-tabs" ref="nav" :style="navStyle">
        <li v-for="tab of tabList" :key="tab.id" :class="tab.id === currentTab ? 'active' : ''"
          @click="handleClickTab(tab.id)">
          {{ tab.name }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      scrollable: false,
      navOffset: 0,
      tabList: [
        { id: 1, name: 1111 },
        { id: 2, name: 2222 },
        { id: 3, name: 3333 },
        { id: 4, name: 4444 },
        { id: 5, name: 5555 },
        { id: 6, name: 6666 }
      ],
      currentTab: 5
    };
  },
  computed: {
    navStyle() {
      return {
        transform: `translateX(-${this.navOffset}px)`
      };
    }
  },
  methods: {
    scrollPrev() {
      const containerSize = this.$refs.navScroll["offsetWidth"];
      const currentOffset = this.navOffset;
      if (!currentOffset) return;
      let newOffset =
        currentOffset > containerSize ? currentOffset - containerSize : 0;
      this.navOffset = newOffset;
    },
    scrollNext() {
      const navSize = this.$refs.nav["offsetWidth"];
      const containerSize = this.$refs.navScroll["offsetWidth"];
      const currentOffset = this.navOffset;
      if (navSize - currentOffset <= containerSize) return;
      let newOffset =
        navSize - currentOffset > containerSize * 2
          ? currentOffset + containerSize
          : navSize - containerSize;
      this.navOffset = newOffset;
    },
    scrollToActiveTab() {
      if (!this.scrollable) return;
      const nav = this.$refs.nav;
      const activeTab = this.$el.querySelector(".active");
      if (!activeTab) return;
      const navScroll = this.$refs.navScroll;
      const isHorizontal = true;
      const activeTabBounding = activeTab.getBoundingClientRect();
      const navScrollBounding = navScroll.getBoundingClientRect();
      const maxOffset = nav.offsetWidth - navScrollBounding.width;
      const currentOffset = this.navOffset;
      let newOffset = currentOffset;
      if (isHorizontal) {
        if (activeTabBounding.left < navScrollBounding.left) {
          newOffset =
            currentOffset -
            (navScrollBounding.left - activeTabBounding.left);
        }
        if (activeTabBounding.right > navScrollBounding.right) {
          newOffset =
            currentOffset +
            activeTabBounding.right -
            navScrollBounding.right;
        }
      }
      newOffset = Math.max(newOffset, 0);
      this.navOffset = Math.min(newOffset, maxOffset);
    },
    update() {
      if (!this.$refs.nav) return;
      const navSize = this.$refs.nav["offsetWidth"];
      const containerSize = this.$refs.navScroll["offsetWidth"];
      const currentOffset = this.navOffset;
      if (containerSize < navSize) {
        const currentOffset = this.navOffset;
        this.scrollable = this.scrollable || {};
        this.scrollable.prev = currentOffset;
        this.scrollable.next = currentOffset + containerSize < navSize;
        if (navSize - currentOffset < containerSize) {
          this.navOffset = navSize - containerSize;
        }
      } else {
        this.scrollable = false;
        if (currentOffset > 0) {
          this.navOffset = 0;
        }
      }
    },
    handleClickTab(tabId) {
      this.currentTab = tabId;
      this.$nextTick(() => {
        this.scrollToActiveTab();
      });
    }
  },
  updated() {
    console.log("update...");
    this.update();
  },
  mounted() {
    window.onresize = () => {
      return (() => {
        this.update();
      })();
    };
    setTimeout(() => {
      this.update();
      this.scrollToActiveTab();
    }, 0);
  }
}
</script>


<style scoped>
* {
  margin: 0;
  padding: 0;
}

.tab-nav-wrap {
  position: relative;
  padding: 0 20px;
  box-sizing: border-box;
}

.nav-prev,
.nav-next {
  position: absolute;
  cursor: pointer;
}

.nav-prev {
  left: 0;
}

.nav-next {
  right: 0;
}

.tabs-nav-scroll {
  overflow: hidden;
}

.nav-tabs {
  white-space: nowrap;
  float: left;
  transition: transform 0.3s;
}

.nav-tabs>li {
  float: none;
  display: inline-block;
  padding: 0 10px;
  cursor: pointer;
}

.nav-tabs>li:hover,
.nav-tabs>li.active {
  background-color: rgb(16, 57, 190);
  color: white;
}
</style>
