<template>
    <div class="undone">
        <h2>未完成事項</h2>
        <el-table :data="todos" border style="width: 75%">
            <el-table-column prop="complete" width="50" size="large">
                <template #default="scope">
                    <el-checkbox type="primary" v-model="scope.row.complete" @click="ok(scope.row)"></el-checkbox>
                </template>
            </el-table-column>
            <el-table-column type="index" label="No." :index="indexMethod" />
            <el-table-column prop="content" label="Todo" width="180" />
            <el-table-column prop="todotime" label="Date" width="150" />
            <el-table-column fixed="right" label="Opt">
                <template #default="scope">
                    <el-button type="primary" @click="Edit(scope.row)" circle><el-icon>
                            <Edit />
                        </el-icon></el-button>
                </template>
            </el-table-column>
        </el-table>
        <div class="block">
            <el-input id="input" v-model="inputTodo" style="width: 300px" size="large" placeholder=" 請輸入代辦事項" clearable />
            <br>
            <label id="datelabel" for="date" style="font-size: 20px;">預計完成</label>
            <el-date-picker id="date" v-model="inputTime" type="date" size="large" placeholder="Pick a Date" format="YYYY-MM-DD"
                value-format="YYYY-MM-DD" />
            <el-button id="new" @click="add" color="#626aef" :dark="isDark" plain>
                {{ editingTodo ? '更新' : '新增' }}事項</el-button>
        </div>
    </div>
    <div class="done">
        <h2>已完成事項</h2>
        <el-table :data="dones" border style="width: 75%">
            <el-table-column type="index" label="No." :index="indexMethod" />
            <el-table-column prop="content" label="Done" width="180" />
            <el-table-column prop="todotime" label="Date" width="180" />
            <el-table-column fixed="right" label="Opt">
                <template #default="scope">
                    <el-button type="danger" @click="Delete(scope.row)" circle><el-icon>
                            <Delete />
                        </el-icon></el-button>
                </template>
            </el-table-column>
        </el-table>
    </div>
</template>
<script>
import { Delete, Edit } from '@element-plus/icons-vue'
import moment from 'moment';
import axios from 'axios';
import { ref } from 'vue'

const inputTime = ref('')

export default {
    data() {
        return {
            inputTodo: "",
            inputTime: "",
            editingTodo: null,
            editingTime: null,
            todos: [{}],
            dones: [{}],
        };
    },

    computed: {
        indexMethod() {
            return index => index + 1;
        }
    },

    mounted() {
        axios.get('http://localhost:8080/todolists/readTodo')//GET Todolist請求
            .then((todoResponse) => {
                const todosArray = todoResponse.data;
                this.todos = todosArray.filter(todo => todo.complete === false);
                this.dones = todosArray.filter(todo => todo.complete === true);
            })
            .catch((err) => console.log(err));
    },

    methods: {
        add: function () {
            if (this.editingTodo) {
                // Update existing todo
                this.editingTodo.content = this.inputTodo;
                this.editingTodo.todotime = this.inputTime;
                // console.log(editingTodo);
                const id = this.editingTodo.id;
                console.log(id);
                axios.put(`http://localhost:8080/todolists/update/${id}`, {
                    todo: { id: this.todoid, content: this.inputTodo, todotime: this.inputTime, complete: false }
                })
                    .then((todoResponse) => {
                        const todosArray = todoResponse.data;
                        this.todos = todosArray.filter(todo => todo.complete === false);
                        this.dones = todosArray.filter(todo => todo.complete === true);
                    })
                this.editingTodo = '';  // Reset editingTodo
                this.editingTime = '';
            } else {
                axios.post('http://localhost:8080/todolists/createTodo', {
                    todo: { content: this.inputTodo, todotime: this.inputTime, complete: false }
                })
                    .then((todoResponse) => {
                        const todosArray = todoResponse.data;
                        this.todos = todosArray.filter(todo => todo.complete === false);
                        this.dones = todosArray.filter(todo => todo.complete === true);
                    })
                console.log({ content: this.inputTodo, todotime: this.inputTime, complete: false });
            }
            this.inputTodo = '';
            this.inputTime = '';
        },

        ok: function (todo) {
            const id = todo.id;
            const completionDate = moment(new Date().toLocaleDateString()).format('YYYY-MM-DD');
            axios.put(`http://localhost:8080/todolists/update/${id}`, {
                todo: { id: this.todoid, content: todo.content, todotime: completionDate, complete: true }
            })
                .then((todoResponse) => {
                    const todosArray = todoResponse.data;
                    this.todos = todosArray.filter(todo => todo.complete === false);
                    this.dones = todosArray.filter(todo => todo.complete === true);
                })
        },
        Edit: function (todo) {
            this.editingTodo = todo;
            this.inputTodo = todo.content;
            this.inputTime = todo.todotime;
        },
        Delete: function (done) {
            const id = done.id;
            console.log(done);
            console.log(id);
            axios.delete(`http://localhost:8080/todolists/deleteTodo/${id}`)
                .then((todoResponse) => {
                    const todosArray = todoResponse.data;
                    this.todos = todosArray.filter(todo => todo.complete === false);
                    this.dones = todosArray.filter(todo => todo.complete === true);
                })
        },
    },
}
</script>

<style scoped>
.undone>input {
    font-size: 20px;
}

.undone {
    font-size: 20px;
    margin-top: 10px;
    margin-bottom: 10px;
    margin-left: 100px;
    margin-right: 1000px;
    padding: 20px;
    border-radius: 10px;
    background-color: rgb(255, 255, 255);
    border-color: rgb(255, 255, 255);
    border-style: solid;
    text-align: left;
    color: rgb(0, 0, 0);
}

.done {
    font-size: 20px;
    margin-top: 10px;
    margin-bottom: 10px;
    margin-left: 100px;
    margin-right: 1000px;
    padding: 20px;
    border-radius: 10px;
    background-color: rgb(255, 255, 255);
    border-color: rgb(255, 255, 255);
    border-style: solid;
    text-align: left;
    color: rgb(0, 0, 0);
}

.block {
    display: block;
    text-align: left;
    margin-top: 10px;
    margin-bottom: -10px;
    margin-right: 10px;
    padding: 20px;
    border-radius: 10px;
    background-color: rgb(255, 255, 255);
    border-color: rgb(255, 255, 255);
    border-style: solid;
    color: rgb(0, 0, 0);
}

#new {
    
    margin-top: 10px;
    margin-bottom: 10px;
    margin-left: 10px;
    margin-right: 10px;
    padding: 20px;
    border-radius: 5px;
    border-style: solid;
}

#input {
    margin-top: 10px;
    margin-bottom: 10px;
    margin-left: 10px;
    margin-right: 10px;
    padding: 20px;
    border-radius: 5px;
    border-style: solid;

}

#date {
    margin-top: 10px;
    margin-bottom: 10px;
    margin-left: 10px;
    margin-right: 10px;
    padding: 20px;
    border-radius: 5px;
    border-style: solid;
}

</style>