The xFusionCorp development team added updates to the project that is maintained under /opt/blog.git repo and cloned under /usr/src/kodekloudrepos/blog. Recently some changes were made on Git server that is hosted on Storage server in Stratos DC. The DevOps team added some new Git remotes, so we need to update remote on /usr/src/kodekloudrepos/blog repository as per details mentioned below:


a. In /usr/src/kodekloudrepos/blog repo add a new remote dev_blog and point it to /opt/xfusioncorp_blog.git repository.


b. There is a file /tmp/index.html on same server; copy this file to the repo and add/commit to master branch.


c. Finally push master branch to this new remote origin.