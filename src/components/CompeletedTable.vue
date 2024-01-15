<template>
    <div class="done">
        <ul >
            <li v-for="(done,index) in dones" >
            {{index+1}}. {{done.content}}
            </li>
        </ul>
    </div>
</template>
<script>
    export default{
    data(){
        return{         
            dones: [{ content: "英文閱讀", complete: true, },],
        };
    },
    methods:{
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
        edit:function(){

        },
        delet:function(){

        },
    },
}
</script>
<style>

</style>