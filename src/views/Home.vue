<template>
    <div>
        <div v-if="showAddTask">
            <AddTask @add-task="addTask"/>
        </div>
        <Tasks @toggle-reminder="toggleReminder" @delete-task="deleteTask" :tasks="tasks"/>
    </div>
</template>

<script>
    import Tasks from "../components/Tasks";
    import AddTask from "../components/AddTask";

    export default {
        name: "Home",
        props:{
            showAddTask: Boolean,
        },
        components: {
            Tasks,
            AddTask
        },
        data() {
            return {
                tasks: [],
            }
        },
        methods:{
            async addTask(task){
                const res = await fetch('api/tasks', {
                    method: 'POST',
                    headers: {
                        'Content-type': 'application/json',
                    },
                    body: JSON.stringify(task)
                })

                const data = await res.json()

                this.tasks = [...this.tasks, data]
            },
            async deleteTask(id){
                if(confirm("R U sure?")){
                    const res = await fetch(`api/tasks/${id}`, {
                        method: 'DELETE'
                    })

                    res.status === 200 ? (this.tasks = this.tasks.filter((task) => task.id !== id)) : alert('Error deleting task')
                }
            },
            async toggleReminder(id){
                const taskToToggle = await this.fetchTask(id)
                const updTask = {...taskToToggle, reminder: !taskToToggle.reminder}

                const res = await fetch(`api/tasks/${id}`, {
                    method: 'PUT',
                    headers: {
                        'Content-type': 'application/json',
                    },
                    body: JSON.stringify(updTask)
                })

                const data = await res.json()

                this.tasks = this.tasks.map((task) => task.id === id ? {...task, reminder: data.reminder} : task)
            },
            async fetchTasks(){
                const res = await fetch("api/tasks")

                return await res.json()
            },
            async fetchTask(id){
                const res = await fetch(`api/tasks/${id}`)

                return await res.json()
            }
        },
        async created(){
            this.tasks = await this.fetchTasks()
        }
    }
</script>

<style scoped>

</style>