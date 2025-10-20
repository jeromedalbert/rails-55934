# Repro app

This is an app to repro the following issue: on macOS, with Rails 8.1 RC1, the
latest Docker Desktop 4.48.0, and the latest Kamal 2.8.0 configured according
to https://github.com/rails/rails/pull/55934, I am not able to deploy the app
using the localhost Docker registry.

Steps to repro:

- Clone this repo
- Run `bundle install`
- In `config/deploy.yml`, set the server web IP to the IP of a server you have
  access to, for example a cheap Hetzner VPS.  
  Note: the IP you see in this repo and the logs below has been released, there
  is no server attached to it.
- Ensure that Docker Desktop is running
- Run `bin/kamal setup`
- Observe the following error:

    ```
    ERROR (SSHKit::Command::Failed): Exception while executing on host 5.78.64.88: docker exit status: 1
    docker stdout: Nothing written
    docker stderr: Error response from daemon: Get "http://localhost:5555/v2/": dial tcp [::1]:5555: connect: connection refused
    ```

    This seemingly happens when the remote server is trying to access the local
    registry.

<details>
<summary>Full logs</summary>

```
~/c/tmp/rails-55934 (main ✔)$ bin/kamal setup
  INFO [b7bb7fff] Running /usr/bin/env mkdir -p .kamal on 5.78.64.88
  INFO [b7bb7fff] Finished in 1.015 seconds with exit status 0 (successful).
Acquiring the deploy lock...
Ensure Docker is installed...
  INFO [0d2101b5] Running docker -v on 5.78.64.88
  INFO [0d2101b5] Finished in 0.168 seconds with exit status 0 (successful).
Build and push app image...
  INFO [16ac0a3f] Running docker --version && docker buildx version as jerome@localhost
  INFO [16ac0a3f] Finished in 0.258 seconds with exit status 0 (successful).
  INFO [b6b16cf5] Running docker start kamal-docker-registry || docker run --detach -p 127.0.0.1:5555:5000 --name kamal-docker-registry registry:3 as jerome@localhost
  INFO [b6b16cf5] Finished in 0.055 seconds with exit status 0 (successful).
  INFO Cloning repo into build directory `/var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/`...
  INFO [0eb4591b] Running /usr/bin/env git -C /var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684 clone /Users/jerome/c/tmp/rails-55934 --recurse-submodules as jerome@localhost
  INFO Resetting local clone as `/var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/` already exists...
  INFO [338f03e5] Running /usr/bin/env git -C /var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/ remote set-url origin /Users/jerome/c/tmp/rails-55934 as jerome@localhost
  INFO [338f03e5] Finished in 0.009 seconds with exit status 0 (successful).
  INFO [d5e3d947] Running /usr/bin/env git -C /var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/ fetch origin as jerome@localhost
  INFO [d5e3d947] Finished in 0.062 seconds with exit status 0 (successful).
  INFO [87cc1880] Running /usr/bin/env git -C /var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/ reset --hard ddecea1ca404c5da2285e45cc45f55cd020e700c as jerome@localhost
  INFO [87cc1880] Finished in 0.014 seconds with exit status 0 (successful).
  INFO [df62dddf] Running /usr/bin/env git -C /var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/ clean -fdx as jerome@localhost
  INFO [df62dddf] Finished in 0.010 seconds with exit status 0 (successful).
  INFO [c6148bbc] Running /usr/bin/env git -C /var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/ submodule update --init as jerome@localhost
  INFO [c6148bbc] Finished in 0.070 seconds with exit status 0 (successful).
  INFO [19a8f83c] Running /usr/bin/env git -C /var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/ status --porcelain as jerome@localhost
  INFO [19a8f83c] Finished in 0.010 seconds with exit status 0 (successful).
  INFO [080d2376] Running /usr/bin/env git -C /var/folders/yy/fdv640jj0dzblhf85z0gpq4r0000gn/T/kamal-clones/myapp-3345103287684/rails-55934/ rev-parse HEAD as jerome@localhost
  INFO [080d2376] Finished in 0.007 seconds with exit status 0 (successful).
  INFO [18277ea6] Running docker buildx inspect kamal-local-registry-docker-container as jerome@localhost
  INFO [18277ea6] Finished in 0.187 seconds with exit status 0 (successful).
  INFO [7f29153d] Running docker buildx build --output=type=registry --platform linux/amd64 --builder kamal-local-registry-docker-container -t localhost:5555/myapp:ddecea1ca404c5da2285e45cc45f55cd020e700c -t localhost:5555/myapp:latest --label service="myapp" --file Dockerfile . 2>&1 as jerome@localhost
 DEBUG [7f29153d] Command: docker buildx build --output=type=registry --platform linux/amd64 --builder kamal-local-registry-docker-container -t localhost:5555/myapp:ddecea1ca404c5da2285e45cc45f55cd020e700c -t localhost:5555/myapp:latest --label service="myapp" --file Dockerfile . 2>&1
 DEBUG [7f29153d]       #0 building with "kamal-local-registry-docker-container" instance using docker-container driver
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #1 [internal] load build definition from Dockerfile
 DEBUG [7f29153d]       #1 transferring dockerfile: 2.46kB done
 DEBUG [7f29153d]       #1 DONE 0.0s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #2 [auth] docker/dockerfile:pull token for registry-1.docker.io
 DEBUG [7f29153d]       #2 DONE 0.0s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #3 resolve image config for docker-image://docker.io/docker/dockerfile:1
 DEBUG [7f29153d]       #3 DONE 4.3s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #4 docker-image://docker.io/docker/dockerfile:1@sha256:b6afd42430b15f2d2a4c5a02b919e98a525b785b1aaff16747d2f623364e39b6
 DEBUG [7f29153d]       #4 resolve docker.io/docker/dockerfile:1@sha256:b6afd42430b15f2d2a4c5a02b919e98a525b785b1aaff16747d2f623364e39b6 done
 DEBUG [7f29153d]       #4 CACHED
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #5 [auth] library/ruby:pull token for registry-1.docker.io
 DEBUG [7f29153d]       #5 DONE 0.0s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #6 [internal] load metadata for docker.io/library/ruby:3.4.5-slim
 DEBUG [7f29153d]       #6 DONE 0.2s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #7 [internal] load .dockerignore
 DEBUG [7f29153d]       #7 transferring context: 916B done
 DEBUG [7f29153d]       #7 DONE 0.0s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #8 [base 1/3] FROM docker.io/library/ruby:3.4.5-slim@sha256:f1cca61013f823406e5ec23d3b8804ee0ba916febbfee089e647db93e8e749c7
 DEBUG [7f29153d]       #8 resolve docker.io/library/ruby:3.4.5-slim@sha256:f1cca61013f823406e5ec23d3b8804ee0ba916febbfee089e647db93e8e749c7 done
 DEBUG [7f29153d]       #8 DONE 0.0s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #9 [base 2/3] WORKDIR /rails
 DEBUG [7f29153d]       #9 CACHED
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #10 [base 3/3] RUN apt-get update -qq &&     apt-get install --no-install-recommends -y curl libjemalloc2 libvips sqlite3 &&     ln -s /usr/lib/$(uname -m)-linux-gnu/libjemalloc.so.2 /usr/local/lib/libjemalloc.so &&     rm -rf /var/lib/apt/lists /var/cache/apt/archives
 DEBUG [7f29153d]       #10 CACHED
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #11 [stage-2 1/3] RUN groupadd --system --gid 1000 rails &&     useradd rails --uid 1000 --gid 1000 --create-home --shell /bin/bash
 DEBUG [7f29153d]       #11 CACHED
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #12 [internal] load build context
 DEBUG [7f29153d]       #12 transferring context: 19.15kB 0.0s done
 DEBUG [7f29153d]       #12 DONE 0.0s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #13 [build 2/5] COPY Gemfile Gemfile.lock vendor ./
 DEBUG [7f29153d]       #13 CACHED
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #14 [build 1/5] RUN apt-get update -qq &&     apt-get install --no-install-recommends -y build-essential git libyaml-dev pkg-config &&     rm -rf /var/lib/apt/lists /var/cache/apt/archives
 DEBUG [7f29153d]       #14 CACHED
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #15 [build 3/5] RUN bundle install &&     rm -rf ~/.bundle/ "/usr/local/bundle"/ruby/*/cache "/usr/local/bundle"/ruby/*/bundler/gems/*/.git
 DEBUG [7f29153d]       #15 CACHED
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #16 [build 4/5] COPY . .
 DEBUG [7f29153d]       #16 DONE 0.0s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #17 [build 5/5] RUN SECRET_KEY_BASE_DUMMY=1 ./bin/rails assets:precompile
 DEBUG [7f29153d]       #17 2.173 Writing application-8b441ae0.css
 DEBUG [7f29153d]       #17 2.173 Writing controllers/hello_controller-708796bd.js
 DEBUG [7f29153d]       #17 2.173 Writing controllers/application-3affb389.js
 DEBUG [7f29153d]       #17 2.173 Writing controllers/index-ee64e1f1.js
 DEBUG [7f29153d]       #17 2.173 Writing application-bfcdf840.js
 DEBUG [7f29153d]       #17 2.173 Writing stimulus.min-4b1e420e.js
 DEBUG [7f29153d]       #17 2.173 Writing stimulus.min-2395e199.js.map
 DEBUG [7f29153d]       #17 2.173 Writing stimulus-importmap-autoloader-64cc03e1.js
 DEBUG [7f29153d]       #17 2.173 Writing stimulus-d59b3b7f.js
 DEBUG [7f29153d]       #17 2.173 Writing stimulus-loading-1fc53fe7.js
 DEBUG [7f29153d]       #17 2.173 Writing stimulus-autoloader-9d447422.js
 DEBUG [7f29153d]       #17 2.173 Writing turbo.min-5fe7bfc3.js.map
 DEBUG [7f29153d]       #17 2.173 Writing turbo.min-c1e82531.js
 DEBUG [7f29153d]       #17 2.173 Writing turbo-00a6c73b.js
 DEBUG [7f29153d]       #17 2.173 Writing actiontext.esm-c376325e.js
 DEBUG [7f29153d]       #17 2.173 Writing actiontext-c9c6c481.js
 DEBUG [7f29153d]       #17 2.173 Writing trix-9a18fe03.js
 DEBUG [7f29153d]       #17 2.173 Writing trix-65afdb1d.css
 DEBUG [7f29153d]       #17 2.173 Writing actioncable.esm-e0ec9819.js
 DEBUG [7f29153d]       #17 2.173 Writing actioncable-ac25813f.js
 DEBUG [7f29153d]       #17 2.173 Writing action_cable-5212cfee.js
 DEBUG [7f29153d]       #17 2.173 Writing activestorage.esm-81bb34bc.js
 DEBUG [7f29153d]       #17 2.173 Writing activestorage-f9e46063.js
 DEBUG [7f29153d]       #17 2.173 Writing rails-ujs-20eaf715.js
 DEBUG [7f29153d]       #17 2.173 Writing rails-ujs.esm-e925103b.js
 DEBUG [7f29153d]       #17 DONE 2.2s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #18 [stage-2 2/3] COPY --chown=rails:rails --from=build /usr/local/bundle /usr/local/bundle
 DEBUG [7f29153d]       #18 DONE 0.9s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #19 [stage-2 3/3] COPY --chown=rails:rails --from=build /rails /rails
 DEBUG [7f29153d]       #19 DONE 0.1s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       #20 exporting to image
 DEBUG [7f29153d]       #20 exporting layers
 DEBUG [7f29153d]       #20 exporting layers 3.3s done
 DEBUG [7f29153d]       #20 exporting manifest sha256:7c773155f83850f457e736235ee669451f06ef6e73533fb94dbad795dc45ed40 done
 DEBUG [7f29153d]       #20 exporting config sha256:0f5a04d971512d393be7d983147246664356a780842e667b5d85d2b7f93ac77d done
 DEBUG [7f29153d]       #20 exporting attestation manifest sha256:3a8e1d877a35ab3ab9dd96cf369cc8024fcba51a8b650efa2f1e9261d53e76d8 done
 DEBUG [7f29153d]       #20 exporting manifest list sha256:444b3a75a16437b77c06a9afc5f624705234ff3f35ccbf218c490f51dc846e9c done
 DEBUG [7f29153d]       #20 pushing layers 0.1s done
 DEBUG [7f29153d]       #20 pushing manifest for localhost:5555/myapp:ddecea1ca404c5da2285e45cc45f55cd020e700c@sha256:444b3a75a16437b77c06a9afc5f624705234ff3f35ccbf218c490f51dc846e9c
 DEBUG [7f29153d]       #20 pushing layers done
 DEBUG [7f29153d]       #20 pushing manifest for localhost:5555/myapp:ddecea1ca404c5da2285e45cc45f55cd020e700c@sha256:444b3a75a16437b77c06a9afc5f624705234ff3f35ccbf218c490f51dc846e9c 0.0s done
 DEBUG [7f29153d]       #20 pushing manifest for localhost:5555/myapp:latest@sha256:444b3a75a16437b77c06a9afc5f624705234ff3f35ccbf218c490f51dc846e9c done
 DEBUG [7f29153d]       #20 DONE 3.4s
 DEBUG [7f29153d]
 DEBUG [7f29153d]       View build details: docker-desktop://dashboard/build/kamal-local-registry-docker-container/kamal-local-registry-docker-container0/ub89cpfg1wvv0rlocnn4svi59
  INFO [7f29153d] Finished in 12.269 seconds with exit status 0 (successful).
  INFO [3f343d8b] Running docker image rm --force localhost:5555/myapp:ddecea1ca404c5da2285e45cc45f55cd020e700c on 5.78.64.88
  INFO [3f343d8b] Finished in 0.177 seconds with exit status 0 (successful).
  INFO [58be8d1d] Running docker pull localhost:5555/myapp:ddecea1ca404c5da2285e45cc45f55cd020e700c on 5.78.64.88
  Finished all in 13.5 seconds
Releasing the deploy lock...
  Finished all in 15.0 seconds
  ERROR (SSHKit::Command::Failed): Exception while executing on host 5.78.64.88: docker exit status: 1
docker stdout: Nothing written
docker stderr: Error response from daemon: Get "http://localhost:5555/v2/": dial tcp [::1]:5555: connect: connection refused
```
</details>
