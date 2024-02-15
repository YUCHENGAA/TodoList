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
        <!-- <button id="new" @click="add" >新增事項</button> -->
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
export default {
    data() {
        return {
            inputTodo: "",
            inputTime: "",
            editingTodo: null,
            editingTime: null,
            todos: [{ content: "和朋友吃飯", todotime: "2024-03-07", complete: false, },],
            dones: [{ content: "英文閱讀", donetime: "2023-12-07", complete: true, },],
        };
    },
    methods: {
        add: function () {
            if (this.editingTodo) {
                // Update existing todo
                this.editingTodo.content = this.inputTodo;
                this.editingTodo.todotime = this.inputTime;
                this.editingTodo = '';  // Reset editingTodo
                this.editingTime = '';
            } else {
                // Add new todo
                this.todos.push({ content: this.inputTodo, todotime: this.inputTime, complete: false });
            }
            console.log(this.todos);
            this.inputTodo = '';
            this.inputTime = '';
        },

        ok: function (todo) {
            const completionDate = new Date().toLocaleDateString();
            console.log(this.todo);
            this.dones.push({ content: todo.content, complete: true, donetime: completionDate});
            console.log(this.dones);

            // Remove the completed todo from the todos array
            const index = this.todos.indexOf(todo);
            if (index !== -1) {
                this.todos.splice(index, 1);
            }
        },
        Edit: function (todo) {
            this.editingTodo = todo;
            this.inputTodo = todo.content;
            this.inputTime = todo.todotime; 
        },
        Delete: function (done) {
            const index = this.dones.indexOf(done);
            console.log(this.dones);
            if (index !== -1) {
                console.log(this.dones);
                this.dones.splice(index, 1);
            }
        },
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