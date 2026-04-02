#!/bin/bash
set -e
KEY="ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAANN0nBOZE2s+P87s8D3nOQPrr0yYfyzgNrMLW57oaG master@homelab"
USER="${SUDO_USER:-$(logname 2>/dev/null || echo pm)}"
HD="$(eval echo ~$USER)"
echo "[*] Kirie SSH bootstrap for $USER"
if command -v pacman &>/dev/null; then
  pacman -S --noconfirm --needed openssh
elif command -v apt-get &>/dev/null; then
  apt-get update -qq && apt-get install -y -qq openssh-server
elif command -v dnf &>/dev/null; then
  dnf install -y openssh-server
fi
mkdir -p "$HD/.ssh" && chmod 700 "$HD/.ssh"
grep -qF "$KEY" "$HD/.ssh/authorized_keys" 2>/dev/null || echo "$KEY" >> "$HD/.ssh/authorized_keys"
chmod 600 "$HD/.ssh/authorized_keys" && chown -R "$USER:$USER" "$HD/.ssh"
systemctl enable --now sshd 2>/dev/null || systemctl enable --now ssh 2>/dev/null
command -v ufw &>/dev/null && ufw allow ssh 2>/dev/null
command -v firewall-cmd &>/dev/null && { firewall-cmd --permanent --add-service=ssh; firewall-cmd --reload; } 2>/dev/null
echo "[+] SSH ready: $USER@$(hostname -I | awk '{print $1}')"
