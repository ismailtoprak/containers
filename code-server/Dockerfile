FROM debian:bookworm

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    curl ca-certificates unzip apt-utils sudo \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# VS Code Server'ı indir ve yükle
RUN curl -fsSL https://code-server.dev/install.sh | sh

# Çalıştırılabilir dosyayı PATH'e ekleyelim
ENV PATH="/home/coder/.local/share/code-server/bin:${PATH}"

# Config dosyalarını kopyala
COPY settings.json /home/coder/.local/share/code-server/settings.json
COPY keybindings.json /home/coder/.local/share/code-server/keybindings.json
COPY extensions.txt /home/coder/extensions.txt

# add user
RUN useradd -ms /bin/bash coder && echo "coder:coder" | chpasswd && adduser coder sudo && echo "coder" > /etc/hostname

# change directory permission
RUN chown -R coder:coder /home/coder

# switch to coder account
USER coder

# Eklentileri yükle
RUN while read -r extension; do \
    code-server --install-extension "$extension"; \
done < /home/coder/extensions.txt

# set default docker path
WORKDIR /home/coder

EXPOSE 8080

# Container başlatıldığında code-server çalıştır
CMD ["code-server", "--bind-addr", "0.0.0.0:8080", "--auth", "none"]
