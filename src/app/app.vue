<template>
    <h3>{{ name }}</h3>

    <div>用户登录</div>
    <UserLogin 
    v-if="!isLoginIn" 
    @login-success="onLoginSuccess" 
    @login-error="onLoginError"/>
    <div>----------------</div>

    <div>创建内容</div>
    <input 
    v-if="isLoginIn"
    type="text" 
    v-model="title" 
    @keyup.enter="createPost"
    placeholder="请输入内容标题"
    >
    <div>---------------------</div>

    <div>内容列表</div>
    <div v-for="post in posts" :key="post.id">
        <input 
        type="text" 
        :value="post.title" 
        @keyup.enter="updatePost($event, post.id)">
        {{ post.title }} - {{ post.content }} -
        <small>{{ post.user.name }}</small>
        <button @click="deletePost(post.id)">删除内容</button>
    </div>
    <div>-----------------</div>
    
    <div>错误提醒</div>
    <div>{{ this.errorMessage }}</div>
</template>

<script>
import {apiHttpClient} from '@/app/app.service'
import UserLogin from '@/app/user/compoents/user-login.vue'
export default {
    data() {
        return {
            name: 'dp-node',
            posts: [],
            errorMessage: '',
            token: '',
            title: '',
            content: ''
        }
    },
    computed: {
        isLoginIn() {
            return this.token ? true : false
        }
    },
    async created() {
        try {
            const response = await apiHttpClient.get('/posts')
            this.posts = response.data
        } catch (error) {
            this.errorMessage = error.message
        }
    },
    methods: {
        onLoginSuccess(data) {
            this.token = data.token
        },
        onLoginError(error) {
            this.errorMessage = error.data.message
        },
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

                this.title = ''

                this.getPosts()

            } catch (error) {
                this.errorMessage = error.message
                
            }
        },
        async updatePost(event, postId) {
            try {
                await apiHttpClient.patch(
                    `/posts/${postId}`
                ,{
                    title: event.target.value
                },
                {
                    headers: {
                        Authorization: `Bearer ${this.token}`
                    }
                })

                this.getPosts()
            } catch (error) {
                this.errorMessage = error.message
            }
        },
        async deletePost(postId) {
            try {
                await apiHttpClient.delete(
                    `/posts/${postId}`
                ,{
                    headers: {
                        Authorization: `Bearer ${this.token}`
                    }
                })
                this.getPosts()
            } catch (error) {
                this.errorMessage = error.message
            }
        }
    },
    components: {
        UserLogin
    }
}
</script>