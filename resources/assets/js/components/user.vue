<template>
    <div class="">
        <div class="panel panel-default">
            <div class="panel-heading"  style="display: -webkit-box;">
                <div style="width: 50%;"><b><i class="fa fa-gears"></i> Danh sách thành viên</b></div>
                <div style="width: 50%;"><input id="searchuser" class="form-control" v-on:keyup="search" type="text" placeholder="Tìm kiếm thành viên" /></div>
            </div>
            <div class="panel-body">
                <div class="box-body table-responsive no-padding">
                    <table class="table table-hover">
                        <tbody>
                        <tr>
                            <th>STT</th>
                            <th>Name</th>
                            <th>Email</th>
                            <th>Money</th>
                            <th>Fbid</th>
                            <th>Ngày Đăng Ký</th>
                            <th v-if="($parent.info.email == 'builuc1998@gmail.com' || $parent.info.email == 'vinguyet6666@asiamovie.info' || $parent.info.level == 3)">Chức Vụ</th>
                            <!--<th v-if="this.$parent.info.level == 0">Cộng Tiền</th>-->
                        </tr>
                        <tr v-for="(list,keyIndex) in listVipID.data">
                            <td>{{keyIndex + 1}}</td>
                            <td>
                                <router-link :to="'/user/'+list.id">
                                    {{list.name}}
                                </router-link>
                            </td>
                            <td>{{list.email}}</td>
                            <td>{{list.money.toLocaleString('vi', {style : 'currency', currency : 'VND'})}}</td>
                            <td>{{list.fbid}}</td>
                            <td>{{list.created_at}}</td>
                            <td v-if="($parent.info.email == 'builuc1998@gmail.com' || $parent.info.email == 'vinguyet6666@asiamovie.info' || $parent.info.level == 3)" @change="chucvu(list.id)">
                                <select :id="'chucvu'+list.id">
                                    <option value="0">Normal</option>
                                    <option value="1">Member</option>
                                    <option value="2">Admin</option>
                                    <option value="3">Supper Admin</option>
                                </select>
                            </td>
                            <!--<td v-if="$parent.info.level == 0"><input v-on:change="congtien(list.id)" type="number" class="form-control" :id="'congtien_'+list.id" /></td>-->
                        </tr>
                        </tbody>
                    </table>
                    </div>
                    <pagination 
                      :total="this.listVipID.total" 
                      :per_page= "this.listVipID.per_page" 
                      :current_page= "this.listVipID.current_page" 
                      :last_page= "this.listVipID.last_page" 
                      :next_page_url= "this.listVipID.next_page_url" 
                      :prev_page_url= "this.listVipID.prev_page_url" 
                      :from= "this.listVipID.from" 
                      :to= "this.listVipID.to" 
                    ></pagination>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import axios from 'axios'
import pagination from './pagination.vue'
export default {
    data() {
        return {
            listVipID: [],
        }
    },
    components: {
        pagination,
    },
    methods:{
        search: function(){
            this.searchuser = $('#searchuser').val();
            axios.get('api/searchuser?key='+this.searchuser).then((response) => {
                this.listVipID = response.data;
            })
        },
        congtien:function(id){
            this.tien = $('#congtien_'+id).val();
            axios.post('api/addmoney',{
                'id':id,
                'tien':this.tien
            }).then((response) => {
               toastr.success(response.data.message); 
               $('#congtien_'+id).val('');
               axios.get('api/alluser').then((response) => {
                    this.listVipID = response.data;
               })
            }).catch(function (error) {
               toastr.error(error.response.data.message);
            });
        },
        toPage(n){
            let $this = this;
            axios.get(this.listVipID.path+'?page='+n)
            .then(function (response){
                $this.listVipID = response.data;
              })
              .catch(function (error) {
                console.log(error);
              });
        },
        chucvu(id){
            var chucvu = $('#chucvu'+id).val();
            var data = {'id':id,'chucvu':chucvu,'__token':$('meta[name="csrf-token"]').attr('content')};
            axios.post('/api/changeChucvu',data)
            .then((response)=>{
                toastr.success(response.data.message);
            })
            .catch((error)=>{
                toastr.error(error.response.data.message);
            })
        }
    },
    mounted() {
        axios.get('api/alluser').then((response) => {
            this.listVipID = response.data;
        })
    },
}
</script>