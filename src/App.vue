<template>
  <div id="app" ref="root">
    <h1>Latest Products</h1>
    <div class="product-list">
      <div v-for="(product, index) in products" :key="index" class="product-list__item">
        <h3>{{product.name}}</h3>
        <p>{{product.price}}</p>
      </div>
    </div>
    <button v-show="hasMore && !isLoading" @click="loadMore" ref="loadMore">Load more</button>
    <p v-if="isLoading">Fetching...</p>
  </div>
</template>

<script>
//eslint-disable-next-line no-console

export default {
  name: "App",
  data() {
    return {
      products: [],
      totalRecords: 0,
      perPage: 10,
      currentPage: 0,
      isLoading: false
    };
  },
  methods: {
    async fakeAsync(timeout = 2000) {
      await new Promise(resolve => setTimeout(resolve, timeout));
    },
    async loadMore() {
      this.currentPage++;
      await this.loadProducts();
    },
    async loadProducts() {
      this.isLoading = true;
      const { items, totalRecords } = await this.getProducts();
      const newItems = items.slice(this.start, this.limit);
      this.products = [...this.products, ...newItems];
      this.totalRecords = totalRecords;
      this.isLoading = false;
    },
    async getProducts() {
      await this.fakeAsync();
      let productItems = [];
      for (var i = 0; i < 200; i++) {
        productItems.push({
          name: `Nike Air ${i}`,
          price: 2000,
          image: "x.jpg"
        });
      }
      return { items: productItems, totalRecords: productItems.length };
    }
  },
  computed: {
    hasMore() {
      const currentlyShowing = this.perPage * (this.currentPage + 1);
      return this.totalRecords > currentlyShowing;
    },
    start() {
      return this.currentPage * this.perPage;
    },
    limit() {
      return (this.currentPage + 1) * this.perPage;
    }
  },
  async created() {
    await this.loadProducts(this.start, this.limit);
  },
  mounted() {
    let options = {
      root: this.$refs.root,
      rootMargin: "0px",
      threshold: 1.0
    };
    let observer = new IntersectionObserver(async entries => {
      if (entries[0].isIntersecting) {
        await this.loadMore();
      }
    }, options);
    const target = this.$refs.loadMore;
    observer.observe(target);
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  width: 60%;
  height: 100vh;
  /* background: blue; */
  margin: 0 auto;
  overflow: auto;
  padding: 20px 10px;
}
.target {
  margin-top: 100vh;
}

h1 {
  text-align: left;
}

.body {
  width: 100%;
}

.product-list {
  /* width:100%; */
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.product-list__item {
  width: 30%;
  height: 200px;
  background: white;
  border: solid 1px black;
  display: flex;
  margin-bottom: 30px;
  padding: 10px;
}
</style>
