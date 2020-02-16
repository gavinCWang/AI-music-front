<template>
    <div>

        <h1>智能音乐平台</h1>
        <el-button @click="initAlbum" type="button" size="small">初始化数据</el-button>
        <el-button type = "button" v-if="!username" @click="showLoginDialog">用户登录</el-button>
        <el-dropdown v-else @command="handleCommand">
            <span class="el-dropdown-link">
              {{username}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
            <el-dropdown-menu slot="dropdown">
                <el-dropdown-item command="c">退出</el-dropdown-item>
            </el-dropdown-menu>
        </el-dropdown>

        <el-table
                :data="albums"
                style="width: 100%">
            <el-table-column
                    prop="album_id"
                    label="ID"
                    width="100">
            </el-table-column>
            <el-table-column
                    prop="album_name"
                    label="专辑名"
                    width="200">
            </el-table-column>
            <el-table-column
                    prop="price"
                    label="价格">
            </el-table-column>
            <el-table-column
                    prop="public_time"
                    label="发布时间">
            </el-table-column>
            <el-table-column
                    prop="cover"
                    label="封面">
                <template slot-scope="item">
                    <div class="img-wrap">
                        <img :src="item.row.cover" width="70" height="70">
                    </div>
                </template>
            </el-table-column>
            <el-table-column
                    fixed="right"
                    label="操作"
                    width="100">
                <template slot-scope="album">
                    <el-button @click="deleteAlbum(album.row)" type="text" size="small">删除</el-button>
                </template>
            </el-table-column>

        </el-table>
        <LoginDialog :loginDialogFormVisible="loginDialogFormVisible" @hideDialog="hideDialog" @successCallback="successCallback"></LoginDialog>
    </div>
</template>

<script>
    import LoginDialog from '../components/login';
    export default {
        name: "AlbumManger",
        components: {
            LoginDialog
        },
        beforeMount() {
            let username = sessionStorage.getItem('username');
            if (username) {
                this.username = username;
            } else {
                this.showLoginDialog();
            }
        },
        data(){
            return {
                maxId:2,
                album:{album_id:'', album_name:'',price:'', singers:''},
                dialogVisible:false,
                albums:[],
                albumsUrl:"http://39.100.99.94:3000/album",
                loginDialogFormVisible: false,

                username: '',
                loading: false,
                searchContent: '',
                singers: [],
                showSingersResults: false,
                showAlbumsResults: false,


            }
        },

        created() {
            console.log("[INFO] Begin created")
            fetch(this.albumsUrl)
                .then(res => res.json())
                .then(bs => this.albums = bs)
            console.log("[INFO] Albums init data"+this.albums)
        },
        methods:{
            initAlbum(){
                console.log("[INFO] Begin init")
                fetch(this.albumsUrl + "/init")
                    .then(res => res.json())
                    .then(bs => this.albums = bs.albums)
                console.log("[INFO] Albums init data"+this.albums)
            },
            deleteAlbum(album){
                console.log("[INFO] albums info "+ album)
                fetch(this.albumsUrl+"/"+album._id,{method:"DELETE"})
                    .then(res=>res.json())
                    .then(()=>{
                        let index = this.albums.findIndex(item => item.album_id == album.album_id)
                        this.albums.splice(index, 1)
                    })
            },
            showLoginDialog() {
                this.loginDialogFormVisible = true;
            },
            hideDialog() {
                this.loginDialogFormVisible = false;
            },
            successCallback(username) {
                this.username = username;
            },
            loginOut() {
                this.username = '';
                sessionStorage.removeItem('username');
                sessionStorage.removeItem('userId');
                this.showLoginDialog();
            },
            handleCommand(command) {
                switch(command){
                    case 'c': this.loginOut();break;
                    default: break;
                }
            }
        }
    }
</script>

<style scoped>

</style>