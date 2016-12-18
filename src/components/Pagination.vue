<style>
  .pagination {
    float:right;
    padding-right:10px;
  }
  .pagination ul {
    list-style-type:none;
  }
  .pagination ul li {
    display:inline-block;
    background:#efefef;
    border-radius:12px;
    height:24px;
    width:24px;
    text-align:center;
    margin-right:2px;
  }
  .pagination ul li:hover{
    background:#2bbb9c;
  }
  .pagination ul li a {
    text-decoration:none;
    display:block;
    width:24px;
    height:24px;
    margin:0 auto;
    padding-top:5px;
    font-size:14px;
    color:#999;
  }
  .pagination ul li a:hover{
    color:#fff;
  }
  .pagination .active {
    background:#2bbb9c;
  }
  .pagination .active  a{
    color:#fff;
  }
  .pagination .disabled{
    background:#fff;
    cursor:default;
    color: #fff !important;
  }
  .pagination>ul {
    float:right;
  }
  .pagination .stastics {
    float:right;
    font-size: 12px;
    padding-top: 5px;
    margin-right: 5px;
    margin-top: 16px;
  }
  .pagination .stastics span{
    color:#2bbb9c;
  }
</style>

<template>
  <div class="pagination-container">
    <template v-if="rowsTotal > 0">
      <div class="pagination">
        <ul>
          <li>
            <a @click="pprev" :class="{'disabled':isFFirst}">&lt&lt</a>
          </li>
          <li>
            <a @click="prev" :class="{'disabled':isFirst}">&lt</a>
          </li>
          <template v-for="num in pageChunk[activeChunck]" >
            <li :class="{'active': num=== page}" @click="nav(num)">
              <a>{{num}}</a>
            </li>
          </template>
          <li>
            <a @click="next" :class="{'disabled':isLast}">&gt</a>
          </li>
          <li>
            <a @click="nnext" :class="{'disabled':isLLast}">&gt&gt</a>
          </li>
        </ul>
        <div class="stastics">共<span>{{rowsTotal}}</span>条，<span>{{pageTotal}}</span>页</div>
      </div>
    </template>
  </div>
</template>

<script>
  module.exports = {
    props: {
      pageSize: {
        type: Number,
        default: 10
      },
      rowsTotal: {
        type: Number,
        required: true
      },
      page: {
        type: Number,
        required: true
      }
    },
    data() {
      return {
        pageChunk: [],
        activeChunck: 0,
        pageTotal: 1,
        isLast: false,
        isFirst: true,
        isFFirst: false,
        isLLast: false,
      }
    },
    watch: {
      rowsTotal: {
        immediate: true,
        handler: function(newVal, oldVal) {
          if (newVal !== oldVal) {
            // init option when rows changes
            this.page = 1
            this.isFirst = true
            this.isLast = false
            this.isFFirst = true
            this.isLLast = false
            this.activeChunck = 0
            this.pageTotal = Math.ceil(this.rowsTotal / this.pageSize)
            let chunck = []
            const length = this.pageTotal + 1
            for (let i = 1; i < length; i++) {
              chunck.push(i)
              if (i % this.pageSize === 0) {
                this.pageChunk.push(chunck)
                chunck = []
              }
            }
            if (chunck.length !== 0) {
              this.pageChunk.push(chunck)
            }
          }
        }
      },
    },
    computed: {
    },
    methods: {
      nav(index) {
        if (index < 1) {
          this.page = 1
        } else if (index > this.pageTotal) {
          this.page = this.pageTotal
        } else {
          this.page = index
        }
        this.activeChunck = Math.floor((this.page - 1) / this.pageSize)
        this.isFirst = this.page === 1
        this.isLast = this.page === this.pageTotal
        this.isFFirst = this.page < this.pageSize
        this.isLLast = this.page > this.pageSize * (this.pageChunk.length - 1)
      },
      next() {
        this.nav(this.page + 1)
      },
      prev() {
        this.nav(this.page - 1)
      },
      pprev() {
        this.nav(this.page - this.pageSize)
      },
      nnext() {
        this.nav(this.page + this.pageSize)
      }
    }
  }
</script>
