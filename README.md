# chanux.me/blog

The theme is added as a git submodule

```
git submodule init --recursive --remote
git submodule update
```

## Docker image

```
docker build -t chanux/hugo . -f hugo.Dockerfile
docker tag chanux/hugo ${_DOCKER_REGISTRY?}/chanux/hugo:0.145.0
docker push ${_DOCKER_REGISTRY?}/chanux/hugo:0.145.0
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
