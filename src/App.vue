<template>
    <div id="app">
        <el-row type="flex" justify="center">
            <el-col :span="6">
                <el-input placeholder="请输入视频目录的url"></el-input>
            </el-col>
        </el-row>
        <el-row type="flex" justify="center">
            <el-col :span="12" v-bind:style="{width:playerOptions.width+'px'}">
                <videoPlayer
                        class="video-player-box"
                         ref="videoPlayer"
                         :options="playerOptions"
                         :playsinline="true"
                         customEventName="customstatechangedeventname"

                         @play="onPlayerPlay($event)"
                         @pause="onPlayerPause($event)"
                         @ended="onPlayerEnded($event)"
                         @waiting="onPlayerWaiting($event)"
                         @playing="onPlayerPlaying($event)"
                         @loadeddata="onPlayerLoadeddata($event)"
                         @timeupdate="onPlayerTimeupdate($event)"
                         @canplay="onPlayerCanplay($event)"
                         @canplaythrough="onPlayerCanplaythrough($event)"

                         @statechanged="playerStateChanged($event)"
                         @ready="playerReadied">
                </videoPlayer>
            </el-col>
        </el-row>

        <el-row type="flex" justify="center">
            <el-col :span="12">
                <el-slider range></el-slider>
                <el-slider type="danger" range></el-slider>
                <el-slider range></el-slider>
            </el-col>
        </el-row>
        <el-row type="flex" justify="center">
            <el-col :span="12">
                <el-input-number size="mini"></el-input-number>
                / 100
            </el-col>
        </el-row>
        <el-row type="flex" justify="center">
            <el-col :span="12">
                <el-button-group>
                    <el-button type="info">上一个视频</el-button>
                    <el-button type="success">播放</el-button>
                    <el-button type="danger">暂停</el-button>
                    <el-button type="primary">下一个视频</el-button>
                </el-button-group>
            </el-col>
        </el-row>

    </div>
</template>

<script>
    import HelloWorld from './components/HelloWorld.vue'
    import 'video.js/dist/video-js.css'
    import {videoPlayer} from 'vue-video-player'


    export default {
        name: 'app',
        components: {
            HelloWorld,
            videoPlayer
        },
        data() {

            return {
                playerOptions: {
                    width: '800',
                    autoplay: true,
                    muted: true,
                    language: 'en',
                    playbackRates: [0.7, 1.0, 1.5, 2.0],
                    sources: [{
                        // type: "video/mp4",
                        // mp4
                        // src: "@/aaa.mp4",
                        src: "http://vjs.zencdn.net/v/oceans.mp4",
                        // webm
                        // src: "https://cdn.theguardian.tv/webM/2015/07/20/150716YesMen_synd_768k_vp8.webm"
                    }],
                    poster: "https://surmon-china.github.io/vue-quill-editor/static/images/surmon-1.jpg",
                }
            }
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }

    .el-row {
        margin-bottom: 20px;
    }

    .video-player-box {
        width: 100%;
    }
</style>
