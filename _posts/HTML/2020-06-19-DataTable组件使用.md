---
layout: post
categories: HTML
title: DataTable组件使用
meta: 前端表格实现可以使用DataTable组件，支持表格动态渲染等。
---
* content
{:toc}

### 正文

看一个结合Vue使用的实例：
```
<div class="wrap-report-new" id="vue-new-exclass-summary-list">
    <div class="wrap-new-report-select">
        <div style="float: right;" v-if="user_type >= 3"><button class="btn btn-success  btn-report-excel" v-on:click="exportExcel">导出</button></div>
        <div class="tab-button-group">
            <template v-if="user_type == 2">
                <button class="btn btn-report actived" v-on:click="getReportList(list_type_group_all)" v-if="list_type == list_type_group_all">查看所有组</button>
                <button class="btn btn-report" v-on:click="getReportList(list_type_group_all)" v-else="list_type != list_type_group_all">查看所有组</button>
                <button class="btn btn-report actived" v-on:click="getReportList(list_type_group_mate)" v-if="list_type == list_type_group_mate">查看本组</button>
                <button class="btn btn-report" v-on:click="getReportList(list_type_group_mate)" v-else="list_type != list_type_group_mate">查看本组</button>
            </template>
            <template v-if="user_type >= 3">
                <button class="btn btn-report actived" v-on:click="getReportList(list_type_net_all)" v-if="list_type == list_type_net_all">查看大区数据</button>
                <button class="btn btn-report" v-on:click="getReportList(list_type_net_all)" v-else="list_type != list_type_net_all">查看大区数据</button>
                <button class="btn btn-report actived" v-on:click="getReportList(list_type_net_group)" v-if="list_type == list_type_net_group">查看小组数据</button>
                <button class="btn btn-report" v-on:click="getReportList(list_type_net_group)" v-else="list_type != list_type_net_group">查看小组数据</button>
                <button class="btn btn-report actived" v-on:click="getReportList(list_type_net_mate)" v-if="list_type == list_type_net_mate">查看组员数据</button>
                <button class="btn btn-report" v-on:click="getReportList(list_type_net_mate)" v-else="list_type != list_type_net_mate">查看组员数据</button>
            </template>
        </div>

        <!--<div class="data-content">-->
        <div style="margin-bottom: 10px;" v-if="list_type != 1">
            <vm-select :optgroups="input.optgroups" :options="input.options" v-model="input.value" ref="typeSelect" :index="index" :childidx="childIdx" :load="load" :multiple="input.multiple" :method="selectChange"></vm-select>
        </div>
    </div>
    <div class="table-content">
        <div style="" class="table-content-2">
            <table class="table-bordered table display report-new-table" id="report-new-table-2">
                <thead>
                <tr>
                    <th style="text-align: center;">大区</th>
                    <th style="text-align: center;">小组组名</th>
                    <th style="text-align: center;">组员姓名</th>
                    <?php foreach ($th_lists as $i => $j): ?>
                        <th><?= $j ?></th>
                    <?php endforeach; ?>
                </tr>
                </thead>
                <tfoot v-show="list_type != 1">
                <tr>
                    <td colspan="3">总计</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                    <td>0</td>
                </tr>
                </tfoot>
                <tbody></tbody>
            </table>
        </div>
        <!--</div>-->
    </div>
</div>

<script>
    var vueReportNewList = new Vue({
        components: {
            'vm-select': {
                props: ['optgroups', 'options', 'value', 'multiple', 'method', 'load', 'index', 'childidx'],
                template: "<select :multiple='multiple' class='selectpicker' data-live-search='true' title='请选择' data-live-search-placeholder='搜索'>"
                    + "<optgroup :label='optgroup.label' v-for='optgroup in optgroups'>"
                    + "<option :value='option.value' v-for='option in optgroup.options'>{{ option.label }}</option></optgroup>"
                    + "<option :value='option.value' v-for='option in options'>{{ option.label }}</option>"
                    + "</select>",
                mounted: function () {
                    var vm = this;
                    $(this.$el).selectpicker({
                        dropupAuto: false,
                        actionsBox: true
                    });
                    $(this.$el).on('changed.bs.select', function () {
                        vm.$emit('input', $(this).val());
                        if (typeof (vm.method) != 'undefined') {
                            vm.method(vm.index, vm.childidx, this.value);
                        }
                    });
                    $(this.$el).on('show.bs.select', function () {
                        if (typeof (vm.load) != 'undefined') {
                            vm.load(vm.index, vm.childidx);
                        }
                    });
                },
                updated: function () {
                    $(this.$el).selectpicker('val', []);
                    $(this.$el).selectpicker('refresh');
                },
                destroyed: function () {
                    $(this.$el).selectpicker('destroy');
                }
            }
        },
        el: '#vue-new-exclass-summary-list',
        data: {
            user_type: <?= $user_data['user_type'] ?>,
            list_type: 1, //1-组员单个数据 21-所有组数据 22-本组组员数据 31-大区数据 32-小组数据（大区） 33-组员数据（所有）
            list_type_teammate: 1,
            list_type_group_all: 21,
            list_type_group_mate: 22,
            list_type_net_all: 31,
            list_type_net_group: 32,
            list_type_net_mate: 33,
            list_loading: false,
            select_list: [],
            list_data_all: [],
            list_data: [],
            list_data_show: [],
            scHeight: false,
            tableColumns_fix:{
                network_name: '',
                group_name:'',
                nickname: ''
            },
            tableColumns_num:{
                0: 0,
                1: 0,
                2: 0,
                3: 0,
                4: 0,
                5: 0,
                6: 0,
                7: 0,
                8: 0,
                9: 0,
                10: 0,
                11: 0,
                12: 0,
                13: 0
            },
            input: {
                optgroups: [],
                options: [],
                value: [],
                multiple: true
            },
            index: 0,
            childIdx: 0,
            search_kefu_all: {network_list: [], group_list: [], member_list: []},
            tableInstance_2: null,
            error_code: 200,
            error_msg: "",
            other: ''
        },
        methods: {
            initReportTable: function () {       //初始化实例
                var that = this;
                var tableColumns = [];
                $.each(that.tableColumns_fix, function (i,n){
                    tableColumns.push({data:i});
                });
                $.each(that.tableColumns_num, function (i,n){
                    tableColumns.push({data:i});
                });

                that.tableInstance_2 = $('#report-new-table-2').DataTable({
                    autoWidth: false,
                    paging: false,
                    searching: false,
                    ordering: true,
                    order: [],
                    aoColumnDefs: [{bSortable: false, aTargets: [0, 1, 2]}],
                    data: [],
                    scrollY: that.scHeight,
                    scrollX: true,
                    scrollCollapse: true,
                    fixedColumns:   {
                        leftColumns: 3,
                    },
                    dom: 'Bfrtip',
                    buttons: [
                        {
                            extend: 'excel',
                            text: 'Save All'
                        }
                    ],
                    columns: tableColumns,
                    language: {
                        emptyTable: "暂无数据",
                        info: "",
                        sInfo: "",
                        infoEmpty: ""
                    }
                });
                that.tableInstance_2.buttons().nodes().hide();      //隐藏按钮
                that.tableInstance_2.on('draw.dtfc', function() {   //调整宽高值
                    if($('#report-new-table-2').width() > $('.dataTables_scrollBody').width()) {
                        $('.wrap-report-new .DTFC_LeftBodyWrapper').height($('.wrap-report-new .DTFC_LeftBodyWrapper').height() - 12);
                        $('.wrap-report-new .DTFC_LeftBodyLiner').height($('.wrap-report-new .DTFC_LeftBodyLiner').height() - 12);
                        $('.wrap-report-new .DTFC_LeftBodyLiner').width($('.wrap-report-new .DTFC_LeftBodyLiner').width() + 1);
                        $('.wrap-report-new .DTFC_LeftFootWrapper').css('top',(parseInt($('.wrap-report-new .DTFC_LeftFootWrapper').css('top')) + 12) + 'px');
                    }
                });
                $(window).on( 'resize.DTFC', function () {   //调整宽高值
                    if($('#report-new-table-2').width() > $('.dataTables_scrollBody').width()) {
                        $('.wrap-report-new .DTFC_LeftBodyWrapper').height($('.wrap-report-new .DTFC_LeftBodyWrapper').height() - 12);
                        $('.wrap-report-new .DTFC_LeftBodyLiner').height($('.wrap-report-new .DTFC_LeftBodyLiner').height() - 12);
                        $('.wrap-report-new .DTFC_LeftBodyLiner').width($('.wrap-report-new .DTFC_LeftBodyLiner').width() + 1);
                        $('.wrap-report-new .DTFC_LeftFootWrapper').css('top',(parseInt($('.wrap-report-new .DTFC_LeftFootWrapper').css('top')) + 12) + 'px');
                    }
                });
            },
            sumReportData: function () {         //统计数据结果
                var api = this.tableInstance_2;
                var that = this;
                var sum_data = {}, sum = [];
                $.each(that.list_data, function(i1, n1) {
                    $.each(n1, function(i2, n2) {
                        if(that.tableColumns_num.hasOwnProperty(i2)) {
                            if(i1 == 0) {
                                sum_data[i2] = parseFloat(n2.total);
                            } else {
                                sum_data[i2] += parseFloat(n2.total);
                            }
                        }
                    });
                });
                $.each(sum_data, function (i,n){
                    sum.push(Math.round(n));
                });
                $(api.table().footer()).find('tr td').each(function (i) {
                    if(i > 0) {
                        $(this).text(sum[i - 1] ? sum[i - 1] : 0);
                    }
                });
            },
            // 获取时:分
            getHourMinute: function (time) {
                var date = new Date(time * 1000);
                var hour = date.getHours();
                hour = hour < 10 ? '0' +hour : hour;
                var minute = date.getMinutes();
                minute = minute < 10 ? '0' +minute : minute;

                return hour +":" + minute;
            },
            // 获取上课时间
            getClassTimeStr: function(time) {
                var start = this.getHourMinute(parseInt(time));
                var end = this.getHourMinute(parseInt(time) + (50 * 60));

                return start +"-" +end;
            },
            // 重载popover
            freshPopover: function () {
                $("[data-toggle='popover']").popover();
            },
            freshReportData: function () {
                if (this.tableInstance_2 === null) {
                    this.initReportTable();
                }
                this.tableInstance_2.clear();
                this.tableInstance_2.rows.add(this.list_data_show);
                this.tableInstance_2.order.neutral();
                this.tableInstance_2.draw();
                if(this.list_type != 1) {
                    this.sumReportData();
                }
                this.freshPopover();
            },
            getReportList: function (list_type) {
                this.list_type = list_type;
                switch (list_type) {
                    case this.list_type_teammate:
                        this.getReportData();
                        this.tableInstance_2.columns([0, 1, 2]).visible(false);
                        break;
                    case this.list_type_group_all:
                        this.getReportData();
                        this.tableInstance_2.columns([0, 1, 2]).visible(false).columns([1]).visible(true);
                        break;
                    case this.list_type_group_mate:
                        this.getReportData();
                        this.tableInstance_2.columns([0, 1, 2]).visible(false).columns([2]).visible(true);
                        break;
                    case this.list_type_net_all:
                        this.getReportData();
                        this.tableInstance_2.columns([0, 1, 2]).visible(false).columns([0]).visible(true);
                        break;
                    case this.list_type_net_group:
                        this.getReportData();
                        this.tableInstance_2.columns([0, 1, 2]).visible(false).columns([0, 1]).visible(true);
                        break;
                    case this.list_type_net_mate:
                        this.getReportData();
                        this.tableInstance_2.columns([0, 1, 2]).visible(true);
                        break;
                }
            },
            getReportData: function () {
                var url = '/data/summary-list?list_type=' + this.list_type;
                var that = this;
                var load_index = layer.msg('正在加载...', {
                    time: 0,
                    icon: 16,
                    shade: 0.2
                });
                $.ajax({
                    url: url,
                    success: function (response, textStatus, jqXHR) {
                        layer.close(load_index);
                        that.error_code = response.code;
                        if(response.code != 200) {
                            that.error_msg = response.msg;
                            that.list_data_all= [];
                            that.search_kefu_all = {network_list: [], group_list: [], member_list: []};
                            swal(response.msg, '', 'warning');
                            return;
                        }

                        if (that.list_type == 33 || that.list_type == 22 || that.list_type == 1) {
                            that.list_data_all = response.data.list_member;
                        } else if (that.list_type == 32 || that.list_type == 21) {
                            that.list_data_all = response.data.list_group;
                        } else if (that.list_type == 31) {
                            that.list_data_all = response.data.list_network;
                        } else {
                            that.list_data_all = [];
                        }
                        that.input.optgroups = [];
                        that.input.options = [];
                        that.search_kefu_all.network_list = [];
                        if (that.list_type == 31 || that.list_type == 32 || that.list_type == 33) {
                            $.each(response.data.kefu_all.network_list, function (i, n) {
                                that.search_kefu_all.network_list.push({'value': 'network_' + n.network_id, 'label': n.network_name});
                                n.network_name = n.network_name + '(' + n.manager_name +')';
                            });
                            that.input.optgroups.push({label: "大区", options: that.search_kefu_all.network_list});
                        }
                        that.search_kefu_all.group_list = [];
                        if (that.list_type == 32 || that.list_type == 33 || that.list_type == 21) {
                            $.each(response.data.kefu_all.group_list, function (i, n) {
                                that.search_kefu_all.group_list.push({'value': 'group_' + n.group_id, 'label': n.group_name});
                                n.group_name = n.group_name + '(' + n.leader_name +')';
                            });
                            that.input.optgroups.push({label: "小组", options: that.search_kefu_all.group_list});
                        }
                        that.search_kefu_all.member_list = [];
                        if (that.list_type == 33 || that.list_type == 22) {
                            $.each(response.data.kefu_all.member_list, function (i, n) {
                                that.search_kefu_all.member_list.push({'value': 'member_' + n.kefu_id, 'label': n.nickname});
                            });
                            that.input.optgroups.push({label: "组员", options: that.search_kefu_all.member_list});
                        }
                    },
                    error: function () {
                        that.search_kefu_all = {network_list: [], group_list: [], member_list: []};
                        layer.close(load_index);
                        swal('请求异常', '', 'warning');
                    }
                });
            },
            selectChange: function () {
                var selected = this.input.value;
                var that = this;
                if (!selected || selected.length == 0) {
                    this.list_data = this.list_data_all;
                } else {
                    var search_new_list = [];
                    $.each(selected, function (i, n) {
                        var split_n = n.split('_');
                        var s_prefix = split_n[0];
                        var s_data = split_n[1];
                        if (s_prefix == 'network') {
                            $.each(that.list_data_all, function (i2, k_data) {
                                if (k_data['network_id'] == s_data) {
                                    search_new_list.push(k_data);
                                }
                            });
                        } else if (s_prefix == 'group') {
                            $.each(that.list_data_all, function (i2, k_data) {
                                if (k_data['group_id'] == s_data) {
                                    search_new_list.push(k_data);
                                }
                            });
                        } else if (s_prefix == 'member') {
                            $.each(that.list_data_all, function (i2, k_data) {
                                if (k_data['kefu_id'] == s_data) {
                                    search_new_list.push(k_data);
                                }
                            });
                        }
                    });

                    that.list_data = search_new_list;
                }
            },
            load: function () {},
            exportExcel: function (){
                this.tableInstance_2.buttons().nodes().click();
            }
        },
        watch: {
            list_data_all: function () {
                this.list_data = this.list_data_all;
            },
            list_data: function () {
                var show_data = [];
                var that = this;
                var box_count = 0;
                var box_content = "";
                $.each(this.list_data, function(i1,n1) {
                    var data_cell = {};
                    $.each(n1, function(i2, n2) {
                        if(that.tableColumns_fix.hasOwnProperty(i2)) {
                            data_cell[i2] = n2;
                        } else if(that.tableColumns_num.hasOwnProperty(i2)) {
                            $.each(n2.box, function(i3, n3) {
                                box_count++;
                                box_content += ' ' +that.getClassTimeStr(i3) +' * ' +n3 +' ';
                            });
                            data_cell[i2] = Math.round(n2.total);
                            box_count = 0;
                            box_content = "";
                        }
                    });
                    show_data.push(data_cell);
                });
                that.list_data_show = show_data;
            },
            list_data_show:function (){
                this.freshReportData();
            }
        },
        mounted: function () {
            this.scHeight = $('.mlayer-content').height() - $('.wrap-new-report-select').height() - $('.wrap-report-new-header').height() - 180;
            this.initReportTable();
            if(this.user_type == 1) {
                this.getReportList(this.list_type_teammate);
            } else if(this.user_type == 2) {
                this.getReportList(this.list_type_group_all);
            } else if(this.user_type >= 3) {
                this.getReportList(this.list_type_net_all);
            }
        }
    });
</script>
```

<br/><br/><br/><br/><br/>
### 参考资料 

Using DataTables Data <https://www.datatables.net/manual/data/>

一篇介绍博文，页面样式不太好：<https://www.cnblogs.com/xiashengwang/p/8087181.html>

这个页面耐看些：<https://www.cnblogs.com/showcase/p/11130463.html>

