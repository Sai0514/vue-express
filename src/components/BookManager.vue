<template>
  <div class="books">
    <h1>Book Manager</h1>
    <el-form :inline="true" :model="book" class="demo-form-inline">
      <el-form-item label="图书名称">
        <el-input v-model="book.name" placeholder="请输入图书名称"></el-input>
      </el-form-item>
      <el-form-item label="图书价格">
        <el-input v-model.number="book.price" placeholder="请输入图书价格"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="addBook">添加</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="books" style="width: 80%">
      <el-table-column prop="_id" label="ID" width="200"></el-table-column>
      <el-table-column prop="name" label="图书名称" width="300"></el-table-column>
      <el-table-column prop="price" label="图书价格"></el-table-column>
      <el-table-column fixed="right" label="操作" width="100">
        <template slot-scope="book">
          <el-button @click="deleteBook(book)" type="text" size="small">删除</el-button>
          <el-button @click="handleEdit(book)" type="text" size="small">修改</el-button>
        </template>
      </el-table-column>
    </el-table>
    <h2>总价格： {{priceTotal}}</h2>

    <el-dialog title="修改信息" :visible.sync="dialogVisible" width="40%">
      <el-form :model="curbook">
        <el-form-item label="图书名称" label-width="80px">
          <el-input v-model="curbook.name"></el-input>
        </el-form-item>
        <el-form-item label="图书价格" label-width="80px">
          <el-input v-model.number="curbook.price"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="confirmEdit">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  name: "BookManger",
  created() {
    fetch(this.url, { method: "GET" })
      .then(res => res.json())
      .then(bks => (this.books = bks.body));
  },
  data() {
    return {
      url: "http://localhost:3000/books",
      maxId: 2,
      book: { name: "", price: "" },
      books: [],
      curbook: {},
      dialogVisible: false
    };
  },
  methods: {
    deleteBook(book) {
      fetch(this.url + "/" + book.row._id, { method: "DELETE" })
        .then(res => res.json())
        .then(() => {
          let index = this.books.findIndex(item => item._id == book.row._id);
          this.books.splice(index, 1);
        });
    },
    addBook() {
      fetch(this.url, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(this.book)
      })
        .then(res => res.json())
        .then(nb => {
          this.books.push(nb.body);
          this.book = { name: "", price: "" };
        });
    },
    handleEdit(book) {
      this.curbook = _.cloneDeep(book.row);
      this.dialogVisible = true;
    },
    confirmEdit() {
      fetch(`${this.url}/${this.curbook._id}`, {
        method: "PUT",
        body: JSON.stringify(this.curbook),
        headers: {
          "content-type": "application/json"
        }
      })
        .then(res => res.json())
        .then(data => {
          let book = data.body;
          let index = this.books.findIndex(item => book._id == item.id);
          this.books.splice(index, 1, book);
          this.dialogVisible = false;
        });
    }
  },
  computed: {
    priceTotal() {
      return this.books.reduce((prev, book) => prev + book.price, 0);
    }
  }
};
</script>

<style scoped>
</style>