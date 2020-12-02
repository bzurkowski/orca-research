# Tools

## Helm

Installation of both Helm versions (v2 and v3) based on [this](https://joachim8675309.medium.com/install-helm3-helm2-on-macos-d65f61509799) article.

Install Helm 2:

```bash
$ brew install helm@2
```

Install Helm 3:

```bash
$ brew install helm
```

Link binaries:

```bash
$ cd /usr/local/bin
mv helm helm3
ln -s /usr/local/opt/helm@2/bin/tiller tiller
ln -s /usr/local/opt/helm@2/bin/helm helm2
```

Now, Helm 2 is accessible via `helm` command, whereas Helm 3 is using `helm3`.

## Go

Download Go release:

```bash
$ wget https://dl.google.com/go/go1.14.2.linux-amd64.tar.gz
```

Extract the release:

```bash
$ tar -C /usr/local -xzf go1.14.2.linux-amd64.tar.gz
```

Add Go binary to system path:

```bash
$ echo "export PATH=\$PATH:/usr/local/go/bin" >> ~/.bash_profile
```
