<template>
    <div class="hello">
        <div class="select-con">
            <!--<el-input type="text" v-model="searchStr" @keyup="onSearch"></el-input>-->
            <input type="text" class="search-input" v-model="searchStr" @keyup="onSearch"/>
            <i class="icon-edge"></i>
        </div>
        <div class="tab">
            <table>
                <thead>
                <tr>
                    <td><input type="checkbox" v-model='checked'></td>
                    <td>序号</td>
                    <td>代码</td>
                    <td>名称</td>
                    <td>数量</td>
                </tr>
                </thead>
                <tbody id="tbody">
                <tr v-for="i in list" :key="`item-${i.index}`">
                    <td><input type="checkbox" v-model="i.checked" @change="checkedChange($event, i)"/></td>
                    <!--<td><el-checkbox v-model="i.checked" @change="checkedChange($event, i)"></el-checkbox></td>-->
                    <td>{{i.index}}</td>
                    <td>{{i.id}}</td>
                    <td>{{i.name}}</td>
                    <td>{{i.num}}</td>
                </tr>
                </tbody>
            </table>
            <div>
                <ul class="pagination">
                    <li v-if="cur !== 1"><a @click="pageClick('pre')" class="page-btn">上一页</a></li>
                    <li @click="onPageNumClick(n)"
                        v-for="n in pageSize"
                        :key="`page-item-${n}`"
                        class="page-item"
                        :class="{active: cur === n}"
                    >
                        <a class="cursor">
                            {{n}}
                        </a>
                    </li>
                    <li v-if="cur < pageSize"><a @click="pageClick('next')" class="page-btn">下一页</a></li>
                </ul>
            </div>
        </div>
    </div>
</template>
<script>
    import data from './assets/data'

    export default {
        name: 'app',
        components: {},
        data() {
            return {
                list: [],
                searchStr: '',
                pageSize: 0, //总页数
                cur: 1       //当前页码
            }
        },
        mounted() {
            data.data.forEach(function (item, index) {
                item.index = index;
                item.checked = false;
            });
            this.list = data.data.slice(0, 5);
            this.pageSize = Math.ceil(data.data.length / 5);
        },
        watch: {},
        methods: {
            onSearch() {
                //最后一个逗号后面的搜索条件
                let str = this.searchStr.split('，').pop().trim();
                //输入框中逗号分开后的已选中项
                let selectedArr = this.searchStr.split('，');
                //根据输入框中已选中的项来改变原数据的选中状态
                data.data.forEach(item => {
                    item.checked = selectedArr.includes(item.name);
                });
                //完成搜索，并且赋值给界面列表
                this.list = data.data.filter(item =>
                    item.name.indexOf(str) !== -1
                    ||
                    item.num.indexOf(str) !== -1
                    ||
                    item.id.indexOf(str) !== -1
                ).slice(0, 5);
            },
            checkedChange(event, i) {
                data.data.forEach(item => {
                    if (item.index === i.index) {
                        item.checked = event.target.checked;
                    }
                });
                this.searchStr = data.data.filter(item => item.checked).map(item => item.name).join(' ， ')
            },
            onPageNumClick: function (message) {
                this.list = data.data.slice(5 * (message - 1), 5 * message);
                this.cur = message;
            },
            pageClick: function (type) {
                if (type === 'pre') {
                    this.cur--;
                } else if (type === 'next') {
                    this.cur++;
                }
                this.onPageNumClick(this.cur);
            }
        }
    }
</script>
<style lang="less">
    .tab {
        width: 353px;
        box-shadow: 3px 3px 20px #888888;
        padding: 1px;
        box-sizing: border-box;
        border-radius: 5px;
    }

    table {
        border-collapse: collapse;
        color: #aaaaaa;
        border-radius: 2px;
        margin-top: 1px;
    }

    table, td, th {
        border: 1px solid #d2d2d2;
    }

    table {
        width: 351px;
        border: 2px solid #d2d2d2;
    }

    table thead td {
        height: 40px;
        line-height: 40px;
        background: #68acee;
        color: #ffffff;
        text-align: center;
    }

    table tbody td {
        height: 32px;
        line-height: 32px;
        text-align: center;
    }

    ul {
        list-style: none;
    }

    ul li {
        display: inline-block;
    }

    .search-input {
        width: 290px;
        height: 28px;
        line-height: 28px;
        border: none;
        outline: none;

    }

    .select-con {
        position: relative;
        padding-right: 30px;
        cursor: pointer;
        display: block;
        width: 320px;
        height: 34px;
        line-height: 34px;
        border-width: 2px;
        border-style: solid;
        background-color: #fff;
        border-radius: 2px;
        border-color: #D2D2D2;
        vertical-align: middle;
        color: #666;
    }

    .icon-edge {
        position: absolute;
        right: 10px;
        top: 50%;
        margin-top: -3px;
        cursor: pointer;
        border-width: 6px;
        border-style: dashed;
        border-color: transparent;
        border-top-color: #c2c2c2;
        border-top-style: solid;
        transition: all .3s;
        -webkit-transition: all .3s;
        display: inline-block;
        width: 0;
        height: 0;

        overflow: hidden;
    }

    .pagination {
        padding: 0;
        margin: 0;
        margin-top: 20px;
        margin-bottom: 20px;
        text-align: center;

        .page-item.active {
            .cursor {
                background-color: #5084b6;
            }
        }
    }

    .page-btn {
        display: inline-block;
        width: 70px;
        height: 36px;
        line-height: 36px;
        text-align: center;
        border-radius: 5px;
        background: #68acee;
        color: #ffffff;
        font-size: 12px;
        margin: 5px 8px;
        cursor: pointer;
    }

    .cursor {
        display: inline-block;
        width: 36px;
        height: 36px;
        line-height: 36px;
        text-align: center;
        border-radius: 2px;
        background: #68acee;
        color: #ffffff;
        font-size: 12px;
        margin: 5px 8px;
        cursor: pointer;
    }
</style>