<script type="text/x-template" id="list-template">
  <div 
    class="list-view" 
    ref="list" 
    @scroll="handleScroll">
    <div     
      class="list-view-phantom"       
      :style="{
         height: contentHeight
      }">
    </div>
    <div
      ref="content"
      class="list-view-content">
      <div
        class="list-view-item"
        :style="{
          height: itemHeight + 'px'
        }"
        v-for="item in visibleData">
        {{ item.value }}
      </div>
    </div>
  </div>
</script>
<div id="app">
  <template>
    <list-view :data="data"></list-view>
  </template>
</div>
<script>
const ListView = {
	name: 'ListView',

  template: '#list-template',
	
	props: {
  	data: {
    	type: Array,
      required: true
    },

    itemHeight: {
      type: Number,
      default: 30
    }
  },
  
  computed: {
  	contentHeight() {
    	return this.data.length * this.itemHeight + 'px';
    }
  },

  mounted() {
    this.updateVisibleData();
  },

  data() {
    return {
      visibleData: []
    };
  },

  methods: {
  	updateVisibleData(scrollTop) {
    	scrollTop = scrollTop || 0;
    	const visibleCount = Math.ceil(this.$el.clientHeight / this.itemHeight);
      const start = Math.floor(scrollTop / this.itemHeight);
      const end = start + visibleCount;
      this.visibleData = this.data.slice(start, end);
      this.$refs.content.style.webkitTransform = `translate3d(0, ${ start * this.itemHeight }px, 0)`;
    },

    handleScroll() {
      const scrollTop = this.$el.scrollTop;
      this.updateVisibleData(scrollTop);
    }
  }
};

new Vue({
  components: {
  	ListView
  },

  data() {
    const data = [];
    for (let i = 0; i < 10000; i++) {
      data.push({ value: i });
    }

    return {
      data
    };
  }
}).$mount('#app')
</script>
<style>
.list-view {
  height: 400px;
  overflow: auto;
  position: relative;
  color: #333;
  border: 1px solid #aaa;
}

.list-view-phantom {
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  z-index: -1;
}

.list-view-content {
  left: 0;
  right: 0;
  top: 0;
  position: absolute;
}

.list-view-item {
  padding: 5px;
  color: #666;
  line-height: 30px;
  box-sizing: border-box;
}
</style>
