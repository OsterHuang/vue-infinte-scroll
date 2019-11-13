<template>
  <div id="app">
    <h1>My Repo List</h1>
    <div class="result">
      <div v-for="(repo, idx) in displayRepoList" :key="repo.id" class="one-data">
        <div class="field"><h3>{{ (idx + 1) }}. {{ repo.title }}</h3></div>
        <div class="field"><div class="field-name">Description:</div>{{ repo.description }}</div>
        <div class="field"><div class="field-name">URL:</div>{{ repo.url }}</div>
      </div>
      <div v-if="isLoading && !isEnd">載入中</div>
      <div v-if="isEnd">已無資料</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      countPerPage: 4,
      pageNo: 1,
      displayRepoList: [],
      repoList: [],
      isLoading: false,
      isEnd: false,
    }
  },
  created() {
    this.queryRepoList()
  },
  mounted() {
    window.addEventListener('scroll', this.scrolling)
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.scrolling)
  },
  methods: {
    async queryRepoList() {
      const list = await fetch('https://api.github.com/users/OsterHuang/repos')
        .then(response => Promise.resolve(response.json()))
      this.repoList = list.map(v => ({
        id: v.id,
        title: v.name,
        description: v.description,
        url: v.html_url
      }))

      this.displayRepoList = this.repoList.slice(0, 4)
    },
    scrolling() {
      if (this.isLoading) return

      if (this.pageNo * this.countPerPage >= this.repoList.length) {
        this.isEnd = true
        return
      }
      if ((window.innerHeight + window.scrollY) >= document.body.offsetHeight) {
        this.isLoading = true
        setTimeout(this.showNextPage, 3000)
        
      }
    }, 
    showNextPage() {
      // const startIdx = this.pageNo * this.countPerPage
      let endIdx = (this.pageNo + 1) * this.countPerPage
      endIdx = endIdx > this.repoList.length ? this.repoList.length : endIdx
      // this.displayRepoList.concat(this.repoList.slice(startIdx, endIdx))
      this.displayRepoList = this.repoList.slice(0, endIdx)

      this.pageNo += 1
      this.$nextTick(() => { this.isLoading = false })
    }
  }
}
</script>

<style lang="stylus">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

div
  box-sizing border-box

#app
  display flex
  flex-direction column
  align-items center

.result
  display flex
  flex-direction column
  align-items center
  width 600px
  padding 12px
  .one-data
    text-align left
    min-height 300px
    min-width 560px

    border-top 1px solid #eee
    border-bottom 1px solid #eee
    > .field
      padding 12px
      .field-name
        display: inline-block
        width 120px
      

</style>
