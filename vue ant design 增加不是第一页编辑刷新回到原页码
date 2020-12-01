---
title: vue ant design 增加不是第一页编辑刷新回到原页码
date: 2017-05-10 10:20:13
categories: vue
---
```bash
引入base方法修改: 
export default {
    inject: ['reload', 'getDefaultPage', 'setDefaultPage'], // 引入方法. reload 用于刷新 getDefaultPage setDefaultPage 取得默认页码, 用于不是第一页编辑刷新回到原页码
增加: 
            pagination: {
                current: this.getDefaultPage(), // 取得默认页码, 用于不是第一页编辑刷新回到原页码
增加:
    created () {
        this.fetch({
            page: this.getDefaultPage(), // 取得默认页码, 用于不是第一页编辑刷新回到原页码
增加:
    methods: {
        handleTableChange (pagination, filters, sorter) {
            // 设置默认页码, 用于不是第一页编辑刷新回到原页码
            this.setDefaultPage(pagination.current);
```
