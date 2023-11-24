### Ceremony

```bash
sudo apt upgrade

sudo apt update

sudo apt-get install build-essential pkg-config libssl-dev clang git-lfs

git clone https://github.com/penumbra-zone/penumbra

sudo apt install cargo

sudo curl https://sh.rustup.rs -sSf | sh -s -- -y

source "$HOME/.cargo/env"

rustup update

screen -S anasayfa

cd penumbra && git fetch && git checkout v0.63.1

cargo build --release --bin pcli

(CTRL A D )

screen -r

cargo run --quiet --release --bin pcli init soft-kms generate

cargo run --quiet --release --bin pcli -- view address

cargo run --release --bin pcli transaction swap --into penumbra 1000test_usd

cargo run --quiet --release --bin pcli view balance

cargo run --quiet --release --bin pcli -- ceremony contribute --phase 1 --bid 145penumbra --coordinator-address penumbra1qvqr8cvqyf4pwrl6svw9kj8******
```
