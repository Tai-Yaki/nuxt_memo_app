<template>
  <section class="container">
    <h1>Mome</h1>
    <table>
      <tr>
        <th>Title</th>
        <td>
          <input
            v-model="title"
            type="text"
            name="title"
            class="title"
            size="40"
            @focus="set_flg"
          />
          <button @click="find">find</button>
        </td>
      </tr>
      <tr>
        <th>Memo</th>
        <td>
          <textarea
            v-model="content"
            name="content"
            cols="50"
            rows="5"
          ></textarea>
        </td>
      </tr>
      <tr>
        <th></th>
        <td>
          <button @click="insert">save</button>
          <transition name="del">
            <button v-if="sel_flg !== false" @click="remove">delete</button>
          </transition>
        </td>
      </tr>
    </table>
    <hr />
    <ul class="list">
      <li v-for="(item, index) in page_items" :key="index">
        <span @click="select(item)">
          {{ item.title }}
          {{ item.created }}
        </span>
      </li>
    </ul>
    <hr />
    <div class="nav">
      <span @click="prev">&lt;prev</span> |
      <span @click="next">next&gt;</span>
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      title: '',
      content: '',
      num_per_page: 7,
      find_flg: false,
      sel_flg: false,
    }
  },
  computed: {
    memo() {
      return this.$store.state.memo.memo
    },
    page_items() {
      if (this.find_flg) {
        const arr = []
        const data = this.$store.state.memo.memo
        data.forEach((element) => {
          if (element.title.toLowerCase().includes(this.title.toLowerCase())) {
            arr.push(element)
          }
        })
        return arr
      } else if (this.sel_flg) {
        return [this.sel_flg]
      } else {
        return this.$store.state.memo.memo.slice(
          this.num_per_page * this.$store.state.memo.page,
          this.num_per_page * (this.$store.state.memo.page + 1)
        )
      }
    },
    page: {
      get() {
        return this.$store.state.memo.page
      },
      set(p) {
        let pg =
          p > (this.$store.state.memo.memo.length - 1) / this.num_per_page
            ? Math.ceil(
                (this.$store.state.memo.memo.length - 1) / this.num_per_page
              ) - 1
            : p
        pg = pg < 0 ? 0 : pg
        this.$store.commit('memo/set_page', pg)
      },
    },
  },
  methods: {
    set_flg() {
      if (this.find_flg || !!this.sel_flg) {
        this.find_flg = false
        this.sel_flg = false
        this.title = ''
        this.content = ''
      }
    },
    insert() {
      this.$store.commit({
        type: 'memo/insert',
        title: this.title,
        content: this.content,
      })
      this.title = ''
      this.content = ''
    },
    select(item) {
      this.find_flg = false
      this.sel_flg = item
      this.title = item.title
      this.content = item.content
    },
    remove() {
      if (!this.sel_flg) return

      this.$store.commit({
        type: 'memo/remove',
        ...this.sel_flg,
      })
    },
    find() {
      this.sel_flg = false
      this.find_flg = true
    },
    next() {
      this.page++
    },
    prev() {
      this.page--
    },
    created() {
      this.$store.commit('memo/set_page', 0)
    },
  },
}
</script>

<style>
.container {
  padding: 5px 10px;
}

h1 {
  font-size: 60pt;
  color: #345980;
}

p {
  padding-top: 5px;
  font-size: 20pt;
}

div {
  font-size: 14pt;
}

pre {
  padding: 10px;
  font-size: 18pt;
  background-color: #efefef;
}

input {
  font-size: 14pt;
  margin: 5px;
}

textarea {
  font-size: 14pt;
  margin: 5px;
}

button {
  font-size: 14pt;
  padding: 1px 10px;
  margin: 5px;
}

hr {
  border-style: none;
  border-top: solid;
  border-width: 5px;
  border-color: #def;
  margin: 20px 0 10px 0;
}

li {
  font-size: 14pt;
  height: 32px;
}

th {
  background-color: #345980;
  color: white;
}

td {
  background-color: aliceblue;
  color: #345980;
  padding: 5px;
}

.nav {
  padding: 0 10px;
  cursor: pointer;
}

.list {
  cursor: pointer;
}

.del-enter-active,
.del-leave-active {
  transition: opacity 0.5s;
}

.def-enter,
.def-leave-to {
  opacity: 0;
}
</style>
