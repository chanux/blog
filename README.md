# chanux.me/blog

The theme is added as a git submodule

```
git submodule init
git submodule update
```

## Dev build

```
hugo --baseURL https://chanux.localhost/blog --destination ~/Workspace/blog/site/blog/
```

Caddy config for testing
```
chanux.localhost {
    root * ~/Workspace/blog/site
    file_server
}
```

## Notes

I made the mistake of renaming `posts` dir to `post`. The theme does not work
with this. So as a quick fix I need to manually change the theme.

```
sed -i '' 's/posts/post/'  themes/mini/layouts/partials/navigation.html
```
