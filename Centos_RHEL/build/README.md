# Centos/RH successful build manual


### Requirements

Clean installation of CentOS Linux release 8 (8.1.1911) minimal

### Preparation

All command are issued under root (ie `su - root`)

1. Misc tools

`dnf -y update`

`dnf groupinstall 'Development Tools' -y`

`dnf install -y openssl-devel git cmake readline-devel jq`

2. Microhttpd & Ninja

`dnf --enablerepo=PowerTools install -y libmicrohttpd-devel ninja-build`

3. Rust

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

Choose 1 (default)

Link to `/usr/local`

`ln -s ~/.cargo/bin/* /usr/local/bin/`

### Build FreeTON

1. Go to build directory (or stay at homedir)

2. Clone git

`git clone https://github.com/tonlabs/net.ton.dev.git`

3. Setup ENV

`cd net.ton.dev/scripts/`

`. ./env.sh`

4. Buld all

`./build.sh`
