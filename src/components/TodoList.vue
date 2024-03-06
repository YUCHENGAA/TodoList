<template>
    <div class="undone">
        <h2>代辦事項 | 預計完成時間</h2>
        <li v-if="todos.length == 0">目前沒有任何的代辦事項</li>
        <ul v-else>
            <li v-for="(todo, index) in todos">
                <input type="checkbox" v-model="todo.complete" @change="ok(todo)" />
                {{ index + 1 }}.{{ todo.content }} | {{ todo.todotime }}
                <el-button type="primary" @click="Edit(todo)" circle><el-icon>
                        <Edit />
                    </el-icon></el-button>
            </li>
        </ul>
        <textarea type="text" rows="5" placeholder="請輸入代辦事項" v-model="inputTodo"></textarea>
        <br>
        <label for="date">預計完成時間</label>
        <input type="date" v-model="inputTime">
        <br>
        <el-button id="new" @click="add">{{ editingTodo ? '更新' : '新增' }}事項</el-button>
    </div>
    <div class="done">
        <h2>已完成項目 | 完成時間</h2>
        <li v-if="dones.length == 0">目前沒有任何已完成的代辦事項</li>
        <ul v-else>
            <li v-for="(done, index) in dones">
                {{ index + 1 }}. {{ done.content }} | {{ done.donetime }}
                <el-button type="danger" @click="Delete(done)" circle><el-icon>
                        <Delete />
                    </el-icon></el-button>
            </li>
        </ul>
    </div>
</template>
<script >
import { Delete, Edit } from '@element-plus/icons-vue'
import moment from 'moment';
import axios from 'axios';

export default {
    data() {
        return {
            inputTodo: "",
            inputTime: "",
            editingTodo: null,
            editingTime: null,
            todos: [{}],
            dones: [{}],
            //todos: [{ no: 1, content: "和朋友吃飯", todotime: "2024-03-07", complete: false, },],
            //dones: [{ no: 1, content: "英文閱讀", donetime: "2023-12-07", complete: true, },],
        };
    },

    mounted() {
        axios.all([
            axios.get('http://localhost:8080/todolists/readTodo'),//GET Todolist請求
            //axios.get('http://localhost:8080/donelists/readDone'),//GET Donelist請求
        ])
            .then(axios.spread((todoResponse, doneResponse) => {//
                // for (int = 0; i < todoResponse.length; i++) {
                //     if (todoResponse.complete == true) {
                //         this.todo = todoResponse.data;
                //     } else {
                //         this.done = todoResponse.data;
                //     }
                // }
                this.todos = todoResponse.data;
                //this.dones = doneResponse.data;
            }))
            .catch((err) => console.log(err));

    },

    computed: {
        todono() {
            return (this.todos.length + 1);
        },

        doneno() {
            return (this.dones.length + 1);
        }
    },

    methods: {
        add: function () {
            if (this.editingTodo) {
                // Update existing todo
                this.editingTodo.content = this.inputTodo;
                this.editingTodo.todotime = this.inputTime;
                // console.log(editingTodo);
                const no = this.editingTodo.no;
                console.log(no);
                axios.put(`http://localhost:8080/todolists/update/${no}`, {
                    todo: { no: this.todono, content: this.inputTodo, todotime: this.inputTime, complete: false }
                })
                    .then((todos) => { this.todos = todos.data; console.log(todos) })
                    .catch((error) => console.log(error))

                this.editingTodo = '';  // Reset editingTodo
                this.editingTime = '';
            } else {
                axios.post('http://localhost:8080/todolists/createTodo', {
                    todo: { no: this.todono, content: this.inputTodo, todotime: this.inputTime, complete: false }
                })
                    .then((todos) => { this.todos = todos.data; console.log(todos) })
                    .catch((error) => console.log(error))
                console.log({ no: this.todono, content: this.inputTodo, todotime: this.inputTime, complete: false });
                // Add new todo
                //this.todos.push({ no: this.todono, content: this.inputTodo, todotime: this.inputTime, complete: false });
            }
            //console.log(this.todos);
            this.inputTodo = '';
            this.inputTime = '';
        },

        ok: function (todo) {
            const no = todo.no;
            console.log(todo);
            console.log(no);
            const completionDate = moment(new Date().toLocaleDateString()).format('YYYY-MM-DD');

            // axios.put(`http://localhost:8080/todolists/update/${no}`, {
            //     done: { no: this.todono, content: todo.content, donetime: completionDate, complete: true }
            // })
            //     .then((dones) => { this.dones = todos.data; console.log(dones) })
            //     .catch((error) => console.log(error))


            axios.delete(`http://localhost:8080/todolists/deleteTodo/${no}`)
                .then((todos) => { this.todos = todos.data; })//console.log(todos)
                .catch((error) => console.log(error));
            // axios.delete('http://localhost:8080/todolists/deleteTodo/0')//DELETE Todolist請求
            //     .then((todos) => { this.todos = todos.data; console.log(todos) })
            //     .catch((error) => console.log(error));

            //POST Donelist請求
            axios.post('http://localhost:8080/donelists/createDone', {
                done: { no: this.todono, content: todo.content, donetime: completionDate, complete: true }
            })
                .then((dones) => { this.dones = dones.data; console.log(dones) })
                .catch((error) => console.log(error))

            //console.log(this.todo);
            // axios.post('http://localhost:8080/donelists/create2', {
            //     done: { no: this.doneno, content: todo.content, donetime: completionDate, complete: true }
            // })
            //     .then((dones) => console.log(dones))
            //     .catch((error) => console.log(error))

            //this.dones.push({ no: this.doneno, content: todo.content, donetime: completionDate, complete: true });
            //console.log(this.dones);
        },
        Edit: function (todo) {
            this.editingTodo = todo;
            this.inputTodo = todo.content;
            this.inputTime = todo.todotime;
        },
        Delete: function (done) {
            const no = done.no;
            console.log(done);
            console.log(no);
            axios.delete(`http://localhost:8080/donelists/deleteDone/${no}`)
                .then((dones) => { this.dones = dones.data; console.log(dones) })
                .catch((error) => console.log(error));
        }

        // Delete: function (done) {
        //     const no = done.doneno;
        //     console.log(done);
        //     console.log(no);
        //     axios.delete('http://localhost:8080/donelists/deleteDone/${index}')
        //         .then((dones) => { this.dones = dones.data; console.log(dones) })
        //         .catch((error) => console.log(error))
        // },
    },
}
</script>
<style scoped>
.undone>input {
    font-size: 20px;
}

#app ul {
    list-style: none;
}

#app li {
    line-height: 2em;
    font-size: 20px;
    list-style: none;
}

#app li input {
    margin-right: 24px;
    line-height: 2em;
    width: 24px;
    height: 20px;
    color: white;
}

input,
textarea,
select {
    padding: 8px 14px;
    border: 1px solid hsl(201, 65%, 19%);
    border-radius: 4px;
    margin-left: 8px;
    outline: none;
    background: hsla(231, 63%, 64%, 0.2);
    color: black;
    font-size: 20px;

}

#new {
    border: none;
    background: linear-gradient(90deg,
            hsl(240deg, 50%, 50%),
            hsl(280deg, 50%, 50%));
    padding: 0.5em 1.4em;
    border-radius: 4px;
    color: white;
    width: 300px;
}
</style>