# WordPress + Podman + Caddy Installer ✨

Automatize a criação, hospedagem e remoção de sites WordPress em containers **Podman rootless**, com proxy reverso automático via **Caddy** e banco de dados **MariaDB**. Tudo isso com **HTTPS gratuito**, sem necessidade de Docker, FTP ou privilégios de root.

---

## 🌐 Pré-requisitos

- Linux (qualquer distro compatível com Podman)
- Podman instalado e funcionando em modo rootless
- Sistema com suporte a `systemd --user`
- Domínio apontando para o IP do servidor (para SSL funcionar com Caddy)

---

## 👤 Criação do usuário e ambiente rootless

Antes de executar qualquer script, crie um usuário dedicado (ex: `site4have`) e execute todos os comandos **dentro da sessão desse usuário**:

```bash
sudo useradd -m usersite
sudo passwd usersitepasswd
login usersite
```

> ⚠️ Todos os scripts devem ser executados como esse usuário, **nunca como root**.

---

## 🛠️ script-base: Instalação do ambiente (executar 1x)

### 1. Clone o repositório

```bash
https://github.com/oliveira903/wordpress-podman-caddy-installer
cd wordpress-podman-caddy-installer
```

### 2. Execute o script-base

```bash
chmod +x script-base
./script-base
```

O que ele faz:

- Cria os diretórios:
  - `/home/site4have/sites` (sites hospedados)
  - `/home/site4have/Caddy/sites` (arquivos .conf do Caddy)
- Cria o arquivo `Caddyfile` principal com `import`
- Cria a rede Podman `shared_network`
- Sobe os containers `mariadb-container` e `caddy`
- Gera serviços `systemd` (modo usuário) para manter os containers ativos

> ⚠️ Execute apenas uma vez após criar o usuário. Não é necessário rodar de novo.

---

## 📦 novo-site: Criação automática de um site WordPress

### 3. Execute o `novo-site`

```bash
chmod +x novo-site
./novo-site
```

Durante a execução:

- Solicita o domínio (ex: `meusite.4have.com.br`)
- Cria banco, usuário e senha com segurança
- Baixa o WordPress e gera `wp-config.php` com `FS_METHOD = direct`
- Corrige permissões e inicializa o container do WordPress
- Cria proxy reverso no Caddy com HTTPS automático
- Recarrega o Caddy e valida conexão com o WordPress

> ✅ Ao final, o site estará acessível e pronto para ser instalado pelo navegador, sem erros de permissão.

---

## ❌ remover-site: Exclusão completa de um site WordPress

### 4. Execute o `remover-site`

```bash
chmod +x remover-site
./remover-site
```

Durante a execução:

- Solicita o domínio
- Valida a senha root do MariaDB
- Remove o banco, usuário e container WordPress
- Apaga arquivos do site localmente
- Remove a configuração do Caddy correspondente
- Recarrega o Caddy automaticamente

> ⚠️ A exclusão é **permanente**. Use com cautela!

---

## 📁 Estrutura do Projeto

```
/home/site4have/
├── sites/                        # Onde os sites ficam
│   └── seusite.dominio.com.br/
├── Caddy/
│   ├── Caddyfile                # Arquivo principal (com import)
│   └── sites/                   # Arquivos de configuração individuais
~/.config/systemd/user/
├── podman-mariadb-container.service
└── podman-caddy.service
```

---

## ⚖️ Licença

Este projeto está licenciado sob a licença [MIT](LICENSE).

---

## 🤝 Contribuições

Pull requests são bem-vindos! Sinta-se livre para abrir issues ou sugerir melhorias.

---

💡 Projeto inspirado pela simplicidade e segurança de ambientes rootless com Podman e Caddy. WordPress nunca foi tão fácil de hospedar! ✨

