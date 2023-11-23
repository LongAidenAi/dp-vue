<template>
    <h3>{{ name }}</h3>
    <div v-for="post in posts" :key="post.id">
        {{ post.title }} - 
        <small>{{ post.user.name }}</small>
    </div>
    <div>{{ this.errorMessage }}</div>
</template>

<script>
import axios from 'axios'
export default {
    data() {
        return {
            name: 'dp-node',
            posts: [],
            errorMessage: ''
        }
    },
    created() {
        axios.get('http://localhost:3000/post123').then(response => {
            console.log(response)
            this.posts = response.data
        }).catch(error => {
            console.log(error.message)
            console.log(error.response)
            this.errorMessage = error.message
        })
    }
}
</script>