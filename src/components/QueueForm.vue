<template>
    <div>
        <b-container>
            <b-alert :show="showAlert">提交成功，请勿重复提交！</b-alert>
            <p>为了保证不在祭频道的视频也能记录，请在开播前在下方提交Youtube视频地址来加入检测队列</p>
            <b-form @reset="onReset" @submit="onSubmit">
                <b-form-group id="group1" label="请输入Youtube视频地址：" label-for="youtube-link">
                    <b-form-input
                            id="youtube-link"
                            placeholder="请输入形如https://www.youtube.com/watch?v=a4XAB7vnlWY的链接"
                            required
                            type="url"
                            v-model="form.link"
                    ></b-form-input>
                </b-form-group>
                <b-button id="button" type="submit" variant="primary">提交</b-button>
            </b-form>
            <p>{{result}}</p>
            <b-row><h4>当前队列</h4></b-row>
            <b-table :items="items" v-if="loaded"></b-table>
        </b-container>
    </div>
</template>

<script>
    import axios from "axios";
    export default {
        data() {
            return {
                form: {
                    link: ""
                },
                result: "",
                items: {},
                loaded: false,
                showAlert: 0
            };
        },
        methods: {
            onReset(evt) {
                evt.preventDefault();
                this.form.link = "";
            },
            getQueue() {
                axios
                    .get("https://matsuri.design/api/videos", {
                        params: {
                            count: false,
                            offset: 0,
                            user: "Queues",
                            limit: 0
                        }
                    })
                    .then(
                        response => (
                            (this.items = response.data.VideoList), (this.loaded = true)
                        )
                    );
            },
            onSubmit(evt) {
                evt.preventDefault();
                let data = this.form.link;
                axios
                    .post("https://matsuri.design/api/queue", {
                        link: data
                    })
                    .then(response => ((this.result = response.result), (this.getQueue()), (this.showAlert = 3)));
            }
        },
        created() {
            this.getQueue();
        }
    };
</script>

<style>
    #button {
        display: block;
        margin: 0 auto;
    }
</style>