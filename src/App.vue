<template>
    <div id="app">
        <el-container>
            <!--<el-header>-->
            <!--<el-menu-->
            <!--:default-active="activeIndex1"-->
            <!--class="el-menu-demo"-->
            <!--mode="horizontal"-->
            <!--@select="handleSelect"-->
            <!--text-color="#000"-->
            <!--active-text-color="#ffd04b">-->
            <!--<el-menu-item index="1">Action Detection Dataset Visualization Tool</el-menu-item>-->
            <!--</el-menu>-->
            <!--</el-header>-->

            <el-container>
                <el-aside>
                    <el-row>数据集来源</el-row>
                    <el-row type="flex">
                        <el-col :span="24">
                            <el-radio-group v-model="data_source">
                                <el-radio-button label="default">默认数据集</el-radio-button>
                                <el-radio-button label="upload" disabled>上传文件</el-radio-button>
                                <el-radio-button label="url" disabled>输入url</el-radio-button>
                            </el-radio-group>
                        </el-col>
                    </el-row>
                    <el-row>
                        <el-col>
                            <el-upload v-if="data_source==='upload'" class="my">
                                <el-button size="small" type="primary">
                                    点击上传
                                </el-button>
                            </el-upload>
                            <el-input placeholder="请输入视频目录的url" v-show="data_source==='url'" class="my"></el-input>
                            <el-select v-if="data_source==='default'" v-model="dataset" placeholder="请选择">
                                <el-option value="activity_net_1_3_new.json"></el-option>
                                <el-option value="activity_net.v1-2.min.json"></el-option>
                                <el-option value="Thumos" disabled>Thumos</el-option>
                            </el-select>
                        </el-col>
                    </el-row>

                    <el-row>视频来源</el-row>
                    <el-row>
                        <el-radio-group v-model="video_source">
                            <el-radio-button label="youtube">Youtube</el-radio-button>
                            <el-radio-button label="fileserver" disabled>文件服务器</el-radio-button>
                        </el-radio-group>
                    </el-row>
                    <el-row>
                        <el-input v-show="video_source==='fileserver'" placeholder="请输入视频文件服务器地址"></el-input>
                    </el-row>

                    <el-row type="flex" justify="center">
                        <el-button type="primary" @click="getData">确定</el-button>
                    </el-row>
                </el-aside>
                <el-main>
                    <el-row type="flex" justify="center">
                        <el-col :span="12" v-show="video_source==='youtube'">
                            <youtube :video-id="video_id" @playing="youtubePlaying" ref="youtube">
                            </youtube>
                        </el-col>
                        <el-col :span="12" v-bind:style="{width:playerOptions.width+'px'}"
                                v-show="video_source==='fileserver'">
                            <videoPlayer
                                    class="video-player-box"
                                    ref="videoPlayer"
                                    :options="playerOptions"
                                    :playsinline="true"
                                    @timeupdate="onPlayerTimeupdate($event)"
                                    customEventName="customstatechangedeventname">

                                <!--@play="onPlayerPlay($event)"-->
                                <!--@pause="onPlayerPause($event)"-->
                                <!--@ended="onPlayerEnded($event)"-->
                                <!--@waiting="onPlayerWaiting($event)"-->
                                <!--@playing="onPlayerPlaying($event)"-->
                                <!--@loadeddata="onPlayerLoadeddata($event)"-->

                                <!--@canplay="onPlayerCanplay($event)"-->
                                <!--@canplaythrough="onPlayerCanplaythrough($event)"-->

                                <!--@statechanged="playerStateChanged($event)"-->
                                <!--@ready="playerReadied">-->
                            </videoPlayer>
                        </el-col>
                    </el-row>
                    <el-row type="flex" justify="center" v-if="idx!==-1">
                        {{ inAction ? "In action" : "Background" }}
                    </el-row>
                    <el-row type="flex" justify="center">
                        <el-col :span="12">
                            <el-input-number size="mini" v-model="idx"></el-input-number>
                            / {{ videos.length }}
                        </el-col>
                    </el-row>

                    <el-row type="flex" justify="center">
                        <el-col :span="4">
                            <span>Progress</span>
                        </el-col>
                        <el-col :span="14">
                            <el-slider v-model="player_process" v-bind:max="video.duration"></el-slider>
                        </el-col>
                    </el-row>

                    <div v-if="idx !== -1">
                        <el-row type="flex" justify="center" v-for="segment in video.annotations">
                            <el-col :span="4">
                                <span>{{ segment.label }}</span>
                            </el-col>
                            <el-col :span="14">
                                <el-slider range v-bind:max="video.duration" v-model="segment.segment"></el-slider>
                            </el-col>
                        </el-row>
                    </div>


                </el-main>
            </el-container>

            <el-footer></el-footer>
        </el-container>


    </div>
</template>

<script>
    import HelloWorld from './components/HelloWorld.vue'
    import 'video.js/dist/video-js.css'
    import {videoPlayer} from 'vue-video-player'


    import axios from 'axios'


    export default {
        name: 'app',
        components: {
            HelloWorld,
            videoPlayer
        },
        data() {

            return {
                data_source: "default",
                video_source: "youtube",
                dataset: "activity_net_1_3_new.json",
                video_id: "",
                player_process: 0,
                videos: [],
                idx: -1,
                database: [],
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
        },
        computed: {
            player() {
                return this.$refs.videoPlayer.player
            },
            youtubePlayer() {
                return this.$refs.youtube.player
            },
            video() {

                if (this.idx !== -1) {
                    this.video_id = this.videos[this.idx];
                    return this.database[this.video_id];
                } else {
                    return {}
                }
            },
            inAction(){
                if(this.video === {} )
                    return false;
                for(let ann of this.video.annotations){
                    console.log(ann);
                    let segment = ann.segment;
                    if (this.player_process > segment[0] && this.player_process < segment[1]){
                        console.log("In action")
                        return true
                    }
                }
                return false
            }
        },
        mounted(){
            this.getData()
        },
        methods: {
            onPlayerTimeupdate(player) {
                console.log(player.currentTime());
                console.log(player.duration());
                this.player_process = player.currentTime();
            },
            getData() {
                if(this.data_source === 'default'){
                    axios.get('/' + this.dataset)
                    .then(response => {
                        this.database = response.data.database;
                        this.videos = Object.keys(this.database);
                        this.idx = 0;
                        console.log(response);
                    });
                }

            },
            setProgress(){
                console.log(this.youtubePlayer)
                this.youtubePlayer.setCurrentTime(this.player_process)
            },
            youtubePlaying() {
                console.log("We are watching");
                try {
                    clearSetInterval(this.timer);
                } catch (e) {
                    ;
                }
                this.timer = setInterval(
                    () => {
                        this.youtubePlayer.getCurrentTime().then((value) => {
                            this.player_process = value;
                        });
                    }
                    , 1000);
            },

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
        /*margin-top: 60px;*/
    }

    .el-row {
        margin-top: 20px;
    }


    .video-player-box {
        width: 100%;
    }

    .my {
        margin-top: 10px;
        margin-bottom: 10px;
    }
</style>
