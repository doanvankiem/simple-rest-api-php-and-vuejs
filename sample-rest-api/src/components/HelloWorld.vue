<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <hr>
    <button class="btn btn-success" v-if="!isAdd" @click="isAdd = !isAdd">Add</button>
    <form @submit.prevent="onSubmit" v-if="isAdd">
      <div class="form-group">
        <label for="title">Title</label>&nbsp;&nbsp;
        <input type="text" id="title" v-model="frmAdd.title"/>
      </div>
      <br>
      <div class="form-group">
        <label for="author">Author</label>&nbsp;&nbsp;
        <input type="text" id="author" v-model="frmAdd.author"/>
      </div>
      <br>
      <div class="form-group">
        <label for="book_description">Description</label>&nbsp;&nbsp;
        <textarea v-model="frmAdd.book_description" id="book_description" cols="30" rows="5"></textarea>
      </div>
      <br>
      <button class="btn btn-success">{{ frmAdd.id === 0 ? 'Save' : 'Update' }}</button>
    </form>
    <hr>
    <table class="table">
      <thead>
        <tr>
          <th>#</th>
          <th>Title</th>
          <th>Author</th>
          <th>Description</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
      <tr v-for="(value, key) of books" :key="key">
        <td>{{value.id}}</td>
        <td>{{value.title}}</td>
        <td>{{value.author}}</td>
        <td>{{value.book_description}}</td>
        <td><a href="" @click.prevent="onEdit(value.id)">Edit</a>&nbsp;&nbsp;<a href="" @click.prevent="onDelete(value.id)">Delete</a></td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      books: [],
      isAdd: false,
      frmAdd: {
        id: 0,
        title: '',
        author: '',
        book_description: '',
      }
    }
  },
  mounted() {
    this.getBooks();
  },
  methods: {
    getBooks: function () {
      axios.get('http://127.0.0.1:8888/index.php/books').then(({ data }) => {
        this.books = data;
      })
    },
    onEdit: function(id) {
      const book = this.books.find(it => it.id === id);
      if (book) {
        this.frmAdd = book;
        this.isAdd = true;
      }
    },
    onDelete: function(id) {
      const book = this.books.find(it => it.id === id);
      if (book) {
        if (confirm(`Do you want remove "${book.title}"`)) {
          axios.delete(`http://127.0.0.1:8888/index.php/books/${id}`).then(({data}) => {
            alert(data.ok);
            this.onResetData();
          });
        }
      }
    },
    onResetData: function() {
      axios.get('http://127.0.0.1:8888/index.php/books').then(({data}) => {
        this.books = data;
      });
      this.frmAdd = {
        title: '',
        author: '',
        book_description: '',
      };
      this.isAdd = false;
    },
    onSubmit: function() {
      let error = false;
      if (this.frmAdd.title === '') error = true;
      if (this.frmAdd.author === '') error = true;
      if (this.frmAdd.book_description === '') error = true;
      if (error) {
        alert("Please fill the form");
        return;
      }
      if (this.frmAdd.id === 0) {
        axios.post('http://127.0.0.1:8888/index.php/books', {...this.frmAdd}).then(({data}) => {
          alert(data.ok);
          this.onResetData();
        })
      } else {
        axios.put('http://127.0.0.1:8888/index.php/books', {...this.frmAdd}).then(({data}) => {
          alert(data.ok);
          this.onResetData();
        })
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .btn {
    display: inline-block;
    margin-bottom: 0;
    font-weight: 400;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    -ms-touch-action: manipulation;
    touch-action: manipulation;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }
  .btn-success {
    color: #fff;
    background-color: #5cb85c;
    border-color: #4cae4c;
  }
  .btn.focus, .btn:focus, .btn:hover {
    color: #333;
    text-decoration: none;
  }
  .btn.active, .btn:active {
    background-image: none;
    outline: 0;
    -webkit-box-shadow: inset 0 3px 5px rgb(0 0 0 / 13%);
    box-shadow: inset 0 3px 5px rgb(0 0 0 / 13%);
  }
  .btn-success:hover {
    color: #fff;
    background-color: #449d44;
    border-color: #398439;
  }
  .btn-success.active, .btn-success:active, .open>.dropdown-toggle.btn-success {
    color: #fff;
    background-color: #449d44;
    background-image: none;
    border-color: #398439;
  }
  table {
    border-collapse: collapse;
    border-spacing: 0;
  }
  table {
    background-color: transparent;
  }
  .table {
    width: 100%;
    max-width: 100%;
    margin-bottom: 20px;
  }
  td, th {
    padding: 0;
  }
  th {
    text-align: left;
  }
  .table>tbody>tr>td, .table>tbody>tr>th, .table>tfoot>tr>td, .table>tfoot>tr>th, .table>thead>tr>td, .table>thead>tr>th {
    padding: 8px;
    line-height: 1.42857143;
    vertical-align: top;
    border-top: 1px solid #ddd;
  }
  .table>thead>tr>th {
    vertical-align: bottom;
    border-bottom: 2px solid #ddd;
  }
  .table>caption+thead>tr:first-child>td, .table>caption+thead>tr:first-child>th, .table>colgroup+thead>tr:first-child>td, .table>colgroup+thead>tr:first-child>th, .table>thead:first-child>tr:first-child>td, .table>thead:first-child>tr:first-child>th {
    border-top: 0;
  }
</style>
