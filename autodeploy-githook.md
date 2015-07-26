# Step 1. Creating Our Repository

Login to your VPS from command line and type the following:
```sh
$ cd /var
$ mkdir repo && cd repo
$ mkdir site.git && cd site.git
$ git init --bare
```
```sh--bare``` means that our folder will have no source files, just the version control.

## Step 2. Hooks

Git repositories have a folder called 'hooks'. This folder contains some sample files for possible actions that you can hook and perform custom actions set by you.

In our repository if you type:
```sh
$ ls
```

You will see a few files and folders, including the 'hooks' folder. So let's go to 'hooks' folder:
```sh
$ cd hooks
```

Now, create the file 'post-receive' by typing:
```sh
$ cat > post-receive
```

When you execute this command, you will have a blank line indicating that everything you type will be saved to this file. So let's type:
```sh
#!/bin/sh
git --work-tree=/var/www/domain.com --git-dir=/var/repo/site.git checkout -f
```

We can check to see if the ref the server is receiving has this format by using an ```sh if ``` construct:
```sh
#!/bin/bash
while read oldrev newrev ref
do
    if [[ $ref =~ .*/master$ ]];
    then
        git --work-tree=/var/www/html --git-dir=/home/demo/proj checkout -f
    fi
done
```

When you finish typing, press 'control-d' to save. In order to execute the file, we need to set the proper permissions using:
```sh
$ chmod +x post-receive
```

#Step 3 - Local Machine

Create your repo:
```sh
$ cd /my/workspace
$ mkdir project && cd project
$ git init
```

Then we need to configure the remote path of our repository. Tell Git to add a remote called 'live':
```sh
$ git remote add live ssh://user@yourdomain.com/var/repo/site.git
```

Here we should give the repository link and not the live folder.

Let's assume that we have some great work ready in this folder. We should do the usual steps of adding the files and commit with a message:
```sh
$ git add .
$ git commit -m "First Commit"
```

Just to remember, the dot after 'git add' means you are adding all files to stage. After 'git commit' we have '-m' which means we will type a message. To complete, we just 'push' everything to the server. We use the 'live' alias that we used when setting the remote.
```sh
$ git push live master
Counting objects: 7, done.Delta compression using up to 4 threads.Compressing objects: 100% (7/7), done.Writing objects: 100% (7/7), 10.56 KiB, done.Total 7 (delta 0), reused 0 (delta 0)To ssh://user@mydomain.com/var/repo/site.git* [new branch]      master -> master
```

If you do not have SSH keys configured, you may have to enter the password of your production server user. You should see something that looks like this:
```sh
Counting objects: 8, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 473 bytes | 0 bytes/s, done.
Total 4 (delta 0), reused 0 (delta 0)
remote: Master ref received.  Deploying master branch...
To demo@107.170.14.32:proj
   009183f..f1b9027  master -> master
```

It's done.