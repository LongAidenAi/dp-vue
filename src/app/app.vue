<template>
    <h3>{{ name }}</h3>
    <input type="text" v-model="title" @keyup.enter="createPost">
    <div v-for="post in posts" :key="post.id">
        {{ post.title }} - {{ post.content }} -
        <small>{{ post.user.name }}</small>
    </div>
    <div>{{ this.errorMessage }}</div>
</template>

<script>
import {apiHttpClient} from '@/app/app.service'
export default {
    data() {
        return {
            name: 'dp-node',
            posts: [],
            errorMessage: '',
            user: {
                name: '用户3',
                password: '123456'
            },
            token: '',
            title: '',
            content: ''
        }
    },
    async created() {
        try {
            const response = await apiHttpClient.get('/posts')
            this.posts = response.data
        } catch (error) {
            this.errorMessage = error.message
        }

        //用户登录
        try {
            const response = await apiHttpClient.post('/login',this.user)
            this.token = response.data.token
            console.log(response.data)
        } catch (error) {
            this.errorMessage = error.message
        }
    },
    methods: {
        async getPosts() {
            try {
                const response = await apiHttpClient.get('/posts')
                this.posts = response.data
            } catch (error) {
                this.errorMessage = error.message
            }
        },
        async createPost() {
            try {
                const response = await apiHttpClient.post('/posts', {
                    title: this.title
                }, {
                    headers: {
                        Authorization: `Bearer ${this.token}`
                    }
                })
                console.log(response)

                this.title = ''

                this.getPosts()

            } catch (error) {
                this.errorMessage = error.message
                
            }
        }
    }
}
</script>