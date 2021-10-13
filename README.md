workflows
# 钉钉 rss 推送机器人
name: DingDingRssBot

on:               
  schedule:                              # 设置定时任务
    - cron: '0 0/1 * * *'
    
jobs:
  build:

    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout codes'
      uses: actions/checkout@v2           # 拉取最新的代码
    - name: Set up Python 3.7.6
      uses: actions/setup-python@v2       # 设置 python 版本
      with:
        python-version: 3.7.6
    - name: Install dependencies          # 安装依赖
      run: |
        cd robot
        pip install -r requirements.txt
    - name: send rss                      
      env:
        DD_WEBHOOK: ${{ secrets.webhook }}
        DD_SECRET: ${{ secrets.secret }}
      run: |
        cd robot
        python rss.py
    - name: Commit
      run: |
        git config --global user.email huxiaohan_hzhc@163.com
        git config --global user.name hxh
        git add .
        git commit -m "update" -a
    - name: Push changes
      uses: ad-m/github-push-action@master 
      with:
        branch: main
        github_token: ${{ secrets.TOKEN }}
其中三个环境变量需要我们自行配置，在项目的 Settings 中的 Secrets 中设置


其中 TOKEN 是 Person access tokens，需要在个人设置中申请，申请时需要勾选 admin:repo_hook,repo,workflow 三个选项。


Person access tokens 主要是用来将修改推送到仓库的，借助 Github Actions 运行完成之后会把代码删除，会导致产生的修改丢失，这样子的话历史记录就无法保存，因此我们需要在每次推送完成之后，将对数据库的修改推送到仓库中。
