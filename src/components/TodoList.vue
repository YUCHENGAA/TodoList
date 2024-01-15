<template>
    <div class="undone">
            <li v-if="todos.length==0">目前沒有任何的代辦事項</li>
            <li v-else-if="todos.length==2">
                <input type="checkbox" :checkbox="todo.complete"/>
                {{todos.content}}
            </li>
            <ul v-else>
                <li v-for="(todo,index) in todos">
                    <input type="checkbox" v-model="todo.complete" @change="ok(todo)"/>
                    {{index+1}}.{{todo.content}}
                    <el-button type="primary" :icon="Edit" circle />
                </li>
            </ul>
            <textarea type="text" rows="5" placeholder="請輸入代辦事項" v-model="inputTodo"></textarea>
            <!-- <label for="date">預計完成時間</label>
            <input type="date"></br> -->
            <button @click="add" >新增事項</button>
            <!-- <button @click="editext = ! editext">
                {{ editext ? "儲存" : "新增" }}事項</button> -->
        </div>
        <div class="done">
            <h2>已完成項目</h2>
        <ul >
            <li v-for="(done,index) in dones" >
            {{index+1}}. {{done.content}}
            <el-button @click="Donelete"><el-icon><Delete /></el-icon></el-button>
            <el-button  :icon="Delete" circle />
            </li>
        </ul>
    </div>
</template>
<script >
import { Delete, Edit } from '@element-plus/icons-vue'
export default{
    data(){
        return{
            // showAnswer: false,
            inputTodo: "",
            todos: [{ content: "和朋友吃飯", complete: false, },],
            dones: [{ content: "英文閱讀", complete: true, },],
        };
    },
    methods:{
        add:function(){
            this.todos.push({ content: this.inputTodo, complete: false, } );
            console.log(this.todos);
            this.inputTodo='';
        },
        ok:function(todo){
            console.log(this.todos);
            this.dones.push({content:todo.content, complete: true, });
            console.log(this.dones);

            // Remove the completed todo from the todos array
            const index = this.todos.indexOf(todo);
            if (index !== -1) {
                this.todos.splice(index, 1);
            }
        },
        Edit:function(){
            // this.todos.inputTodo(this.todos);
            // console.log(this.todos);
        },
        // Delete:function(){
        //     //this.dones.Remove
        // },
    },
}
</script>
<style scoped>
    .undone > input {
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
  width: 355px;
}
</style>