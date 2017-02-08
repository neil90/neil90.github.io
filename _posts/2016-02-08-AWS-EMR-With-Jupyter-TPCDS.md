---
layout:     post
title:      AWS EMR with TPCDS/Jupyter
date:       2016-02-07
summary:    Launching EMR Cluster with TPCDS Data and Jupyter Notebook
categories: AWS
---

<p>Amazon has a pretty robust bootstrap script to install and setup Jupyter notebook located <a href="https://aws.amazon.com/blogs/big-data/running-jupyter-notebook-and-jupyterhub-on-amazon-emr/">here</a>. Script does everything I want however it seemed to add alot of time to the startup process and I really don't need all the other options. Currently I don't use any extra python packages when using Spark.</p>

<script src="https://gist.github.com/neil90/4e2a3a545ed37a02840307a38f7e2aeb.js"></script>
