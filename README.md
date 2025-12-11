# 🚀 Sistema de Instalador Centralizado para Múltiplos Aplicativos

Este projeto descreve como criar um **launcher/instalador centralizado**, capaz de:

- Exibir automaticamente todos os seus aplicativos disponíveis.
- Instalar e atualizar apps com 1 clique (usando .exe ou .msi).
- Sincronizar a lista de aplicativos direto de um servidor gratuito (Nextcloud).
- Permitir adicionar novos apps sem atualizar o launcher do usuário.
- Utilizar um **App Administrador** para fazer uploads e gerenciar tudo.
- Operar sem hospedagem paga — apenas o Nextcloud gratuito.

O objetivo é fornecer aos usuários um **único instalador**, enquanto você mantém um sistema dinâmico e totalmente controlável por um painel administrador.

---

# 🧱 Arquitetura do Sistema

O sistema é dividido em **3 componentes principais**:

---

## 1️⃣ Servidor (Nextcloud Free)

O Nextcloud será usado como seu servidor gratuito para armazenar:

- O arquivo central `list.json`, contendo a lista dos apps.
- Instaladores (.msi, .exe, .zip, etc).
- Ícones.
- Arquivos de versão.

### Estrutura recomendada:

```
/Launcher/
    list.json
    /apps/
        app1_v1.0.0.msi
        app2_v2.1.0.exe
    /icons/
        app1.png
        app2.png
```

O Nextcloud inclui suporte **nativo a WebDAV**, que será usado pelo App Administrador para fazer uploads automáticos.

---

## 2️⃣ Launcher (App Instalador do Usuário)

O **launcher é o app principal** que o usuário baixa uma vez e nunca mais precisa atualizar.

Funções do launcher:

- Baixar a lista de apps (`list.json`) do Nextcloud.
- Exibir automaticamente todos os aplicativos disponíveis.
- Permitir instalar apps com 1 clique.
- Detectar e instalar atualizações.
- Executar instaladores (.exe / .msi).
- Instalar silenciosamente via `msiexec` quando possível.

O launcher **não precisa ser atualizado** quando novos apps forem adicionados, porque toda a lista vem do servidor.

---

## 3️⃣ App Administrador (Painel de Controle)

O app administrador é usado **por você** para gerenciar os apps.

Ele permite:

- Fazer upload de novos instaladores para o Nextcloud via WebDAV.
- Gerar ou editar o `list.json`.
- Enviar atualizações de apps (novas versões).
- Adicionar ícones, descrições e informações extras.
- Remover apps do serviço.

Com isso, é possível adicionar apps novos que **aparecem imediatamente** para todos os usuários.

---

# 🔁 Fluxo de Funcionamento

## 📥 1. Usuario abre o launcher
O launcher:

1. Faz download do arquivo `list.json`.
2. Lê a lista de aplicativos.
3. Exibe todos os apps disponíveis.
4. Permite instalar/atualizar com um clique.

---

## 📦 2. Instalação de Aplicativos

O launcher baixa o instalador (.exe/.msi) e o executa.

### Para instalar MSI silenciosamente:

```
msiexec /i app1_v1.0.0.msi /quiet /norestart
```

### Para instalar um EXE:

```
Process.Start("app_installer.exe")
```

O app é instalado normalmente no Windows.

---

## 🔄 3. Atualização de Aplicativos

Se uma nova versão estiver definida no `list.json`, o launcher:

- Baixa o novo instalador.
- Executa o MSI silenciosamente, que substitui a versão antiga.
- Para EXE, segue o que o instalador permitir.

---

## 📤 4. Como adicionar novos apps (App Administrador)

Usando o painel admin:

1. Você escolhe o arquivo do instalador (.msi/.exe).
2. O app envia via WebDAV para:

```
/Launcher/apps/novoApp_v1.0.0.msi
```

3. O app administrador edita o `list.json`.

### Exemplo de `list.json`:

```json
{
  "apps": [
    {
      "id": "editorTexto",
      "name": "Editor de Texto",
      "version": "1.0.0",
      "installer": "apps/editorTexto_v1.0.0.msi",
      "description": "Editor leve e rápido.",
      "icon": "icons/editorTexto.png"
    },
    {
      "id": "calculadora",
      "name": "Calculadora Ultra",
      "version": "2.0.0",
      "installer": "apps/calculadora_v2.0.0.exe",
      "description": "Uma calculadora avançada.",
      "icon": "icons/calculadora.png"
    }
  ]
}
```

Assim que você envia o novo arquivo, **todos os usuários veem o novo app instantaneamente**.

---

# 🛠 Tecnologias Recomendadas

## 🖥 Servidor
- Nextcloud (gratuito)
- WebDAV habilitado

## 💻 Launcher (App Instalador)
Pode ser criado em:

- **C# — WinForms ou WPF (recomendado para Windows)**
- Python + Tkinter/PySide
- Electron.js
- Java

Tarefas essenciais:

1. Baixar JSON
2. Processar lista
3. Download de instaladores
4. Execução via:
   - `msiexec`
   - `Process.Start()`

---

## 🛠 App Administrador
Pode ser criado em:

- C#
- Python
- Electron.js

Requisitos:

- Cliente WebDAV
- Editor JSON
- Upload automático de arquivos
- Interface CRUD para apps

---

# 📁 Estrutura Geral do Projeto

```
/Projeto-Launcher
    /LauncherApp         # App do usuário final
    /AdminApp            # Painel para administrador
    /Servidor-Nextcloud  # Estrutura remota com arquivos
```

---

# 🔧 Atualização Automática do Launcher

O launcher **não precisa ser atualizado**, porque:

- Não contém apps embutidos nele.
- A lista vem do servidor (`list.json`).
- Ao adicionar apps → aparecem automaticamente.
- Ao remover apps → somem automaticamente.
- Ao atualizar apps → launcher instala a nova versão.

---

# 📦 Por que usar MSI?

Vantagens do MSI:

- Instalações silenciosas:
  ```
  msiexec /i app.msi /quiet
  ```
- Atualizações automáticas
- Substituição automática de versões antigas
- Integração nativa com o Windows Installer

---

# 🎯 Resumo Final

Com esta arquitetura, você terá:

- Um único instalador para o usuário.
- Sistema dinâmico para adicionar apps automaticamente.
- Zero custos de hospedagem.
- Atualizações automáticas.
- Um painel admin que controla tudo.
- Uma solução profissional, modular e escalável.

---

# ✔ Status

Sistema totalmente viável e recomendável para quem cria muitos apps.

---
