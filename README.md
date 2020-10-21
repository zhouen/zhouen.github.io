# EN's Blog Repo
---

## Install Dependencies
```sh
npm install
```

## Themes

- Install `next` theme
```sh
cd zhou-en.github.io
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

- Update a theme
```sh
cd themes/next
git pull
```

- Use the existing theme config
```sh
# make a backup
cp theme/next/_config.yml theme/next/_config_bak.yml
rm theme/next/_config.yml
cp ./_next_theme_config.yml theme/next/_config.yml
```

## Add A New Post
- In the project root directory:
```sh
hexo new post "This is a new post"
```

- There will be a Markdown post created in `source/_posts` called `this-is-a-new-post.md`, add your article content in this file

## Generate blog site
```sh
hexo generate
```

## Deploy your site
```sh
hexo clean && hexo deploy
```
[If](If) there is any `yawn` related error, best approach is to remove the `.deploy_git` directory and rerun the deployment command.

## Start from Scratch
If you start your blog site from scratch using hexo, here are the steps to
create the project:
- Create your repository, if you want to use the `github page` to host it:
    - create your repository on github with the name: `username.github.io`
    - create the following branches:
        - `master`
        - `source`
- Clone the repository and initialize it using hexo:
```sh
cd your_repo_dir
hexo init
```
- In the `_config.yml` update the `deploy` section to:
    ```yml
    deploy:
        type: git
        repo: your_repo_address
        branch: master
        message: 'Blog site was updated!'
    ```






