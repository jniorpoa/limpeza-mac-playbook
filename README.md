# Limpeza e auditoria do Mac com Claude Code

Este é o roteiro de uma limpeza que liberou cerca de 1,1 TB num MacBook (de 450 GB livres para 1,6 TB) sem perder nada que estivesse em uso. Quem faz o trabalho é o Claude Code. O seu papel é autorizar cada rodada.

O ganho depende do que estiver no seu Mac, então o seu resultado vai ser outro. O que importa é o método: medir tudo antes, apagar só com autorização e registrar cada passo.

## Antes de começar

1. **Instale o Claude Code:** <https://claude.com/claude-code>.
2. **Dê Acesso Total ao Disco ao Terminal** (ou ao app que roda o Claude): Ajustes do Sistema > Privacidade e Segurança > Acesso Total ao Disco. Sem isso, metade da pasta pessoal fica invisível e a medição sai errada.
3. **Faça backup.** Ligue o Time Machine num disco externo, se puder. Sem backup, o que sair da Lixeira não volta.
4. **Crie uma pasta para os registros e abra o Claude nela:**

   ```sh
   mkdir -p ~/limpeza-mac && cd ~/limpeza-mac && git init && claude
   ```

5. Cole o prompt abaixo.

## O prompt

```text
Quero limpar e auditar este Mac (storage, performance, apps e segurança). Trabalhe por fases e pare ao fim de cada uma para eu decidir.

REGRAS (valem para a sessão inteira):
- Nada é apagado sem a minha autorização explícita, item por item ou por lista que eu aprovar.
- Apagar = mover para a Lixeira. Quem esvazia a Lixeira sou eu.
- Antes de apagar algo como "duplicado", confirme a cópia por hash (SHA-1 ou checksum), nunca só pelo nome ou pelo tamanho.
- Não use sudo. Quando algo exigir senha, me passe o comando para eu rodar.
- Meça só tamanhos e nomes. Não abra e-mails, mensagens, fotos nem histórico.
- Não mexa em ~/Library/Mobile Documents, na Fototeca por dentro, nem dentro de pacotes .fcpbundle, .photoslibrary ou .app.
- Separe o que você conferiu nesta sessão do que é suposição.

FASE 1: diagnóstico, só leitura
- Modelo, macOS, disco (df), memória, swap, bateria, estado do backup (tmutil).
- Os maiores consumidores da pasta pessoal (du), incluindo ~/Library.
- Caches que se regeneram sozinhos: navegador, npm, pip, uv, Homebrew, Spotify etc.
- Dependências de projetos parados: node_modules, .venv, .next, __pycache__, com a data do último uso.
- Duplicatas exatas de arquivos com 50 MB ou mais, por hash. Salve a lista em duplicatas-AAAA-MM-DD.json.
- Instaladores, ISOs e DMGs em Downloads e na Mesa.
- Modelos locais de IA (Ollama, Whisper, Chrome).
- Apps sem uso, VPNs acumuladas, agentes e daemons de terceiros (launchctl), processos com CPU alta.
- Atualizações pendentes do macOS e do Homebrew.
- Cuidado com ilusões de tamanho: Docker.raw é esparso; clones APFS e arquivos do iCloud aparecem duas vezes no du. Confira o espaço real antes de prometer ganho.
Entregue RELATORIO-AAAA-MM-DD.md com uma tabela: item, ganho estimado, risco, como fazer. Avise onde os ganhos se sobrepõem.

FASE 2: limpeza em rodadas
- Comece pelo risco zero (caches) e avance para o risco maior só com o meu ok.
- Em cada rodada, mostre a lista antes, execute e depois confira o espaço livre.
- Toda movimentação vai para um log organizacao-N-AAAA-MM-DD.tsv (origem <TAB> destino).
- Para remover um app, tire também os restos: Application Support, LaunchAgents/Daemons, helpers, extensões de rede e perfis de VPN.
- Cópia única (sem outro lugar confirmado) é sempre risco médio: pergunte antes.

FASE 3: apps e segurança, só leitura
- Assinatura dos apps (Gatekeeper), apps só Intel (Rosetta), versões atrasadas.
- FileVault, SIP, firewall, portas abertas, perfis MDM, kexts de terceiros.
- Segredos em texto puro em ~/.zshrc, ~/.zshenv, .env e ~/.aws. Só aponte onde estão, não mostre os valores.
- Software de acesso remoto e VPNs que eu não uso.

FASE 4: depois de reiniciar
- Confira o que ainda roda, o que sobrou dos apps removidos, o espaço livre e a Lixeira.

REGISTRO
Mantenha ESTADO-AAAA-MM-DD.md sempre atualizado: resultado (livre antes e depois), o que foi removido de vez, onde as coisas ficaram e pendências em ordem de prioridade. Faça commit no git ao fim de cada fase. Se a sessão cair, eu retomo dizendo "leia o ESTADO e continue".

Comece pela Fase 1.
```

## Dicas

- **Em sessões longas,** peça "atualize o ESTADO" antes de fechar. Na próxima sessão, diga "leia o ESTADO-… e continue".
- **Ganho grande costuma vir de** vídeos duplicados, pastas de projetos antigos, Downloads esquecido e caches de desenvolvimento.
- **Desconfie de número grande demais:** se o `du` somar mais do que o disco usa, são clones ou arquivos do iCloud.
- **Carteira de cripto, autenticador, chaves SSH e `.pem`:** não remova sem confirmar que existe outra cópia ou a seed guardada.
- **Os arquivos desta pasta** vão ter caminhos e nomes dos seus arquivos. Não publique.

## Aviso

Use por sua conta e risco. O roteiro foi feito para não apagar nada sem a sua autorização, mas quem aprova cada remoção é você. Faça backup antes.

## Contribua

Achou um cache que vale limpar, uma armadilha nova ou um erro? Abra uma issue ou um pull request.

## Licença

[MIT](LICENSE)
