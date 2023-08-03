# Disclaimer

Esse tutorial encontra-se também no nosso repositório do [gitlab](https://gitlab.com/bot-do-jao/decidim-brisa/) que é o principal ambiente de desenvolvimento. Esse repositório por vezes será usado com foco na documentação do processo.

# Tutorial configuração do ambiente

O tutorial oferecido pela wiki do repositório é muito bom, mas eu identifiquei algumas etapas que merecem comentários adicionais. Por isso, comentarei seção a seção do que eu achei relevante.

## Versões 

Essa parte aqui segundo a própria documentação diz que a versão importa. Entretanto, a principal delas é a versão do ruby, que no próprio tutorial ja instala a versão correta.
Obs: a versão de vários pacotes que vcs baixarem no terminal pode e provavelmente vai estar errada (como o node, que no ubuntu a versão empacotada é as 12.xx.xx, bem abaixo do pedido) então verifiquem o que ja tem baixado e atualizem depois.

- Git - versão 2.34 ou superior
- Ruby - versão 3.0.4
- NodeJS - versão 16.18.x
- NPM - versão 8.19.x
- Docker
- Docker Compose

## Ruby

Aqui o tutorial deles é perfeito, vou finalmente colocar o link e podem seguir da instalação do rbenv até finalizar ruby

https://gitlab.com/lappis-unb/decidimbr/decidim-govbr/-/wikis/On-board-decidim-govbr#configurando-ambiente

## Node

Aqui o tutorial apenas não funcionou para mim. Peço que desconsiderem ele e usem a do proprio ubuntu empacotada junto do seguinte comando

```bash
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
```

Obs: tenha o npm instalado antes de usar esse comando

## Docker e Docker Compose

Para docker, eu segui o tutorial oficial que instala o docker engine. Seguindo o seguinte link. 

https://docs.docker.com/engine/install/ubuntu/#set-up-the-repository

Depois, siga esses passos para a pós instalação

https://docs.docker.com/engine/install/linux-postinstall/

e por fim, para o docker compose

https://docs.docker.com/compose/install/linux/

(Antes do download do docker compose, use o comando que checa a versão, as vezes ele ja esta instalado)


## Instalando as dependências

Após a parte de docker, ja entramos na configuração de ambiente, felizmente essa parte não tem nenhum comentario relevante, nem link externo, basta voltar para o [link](https://gitlab.com/lappis-unb/decidimbr/decidim-govbr/-/wikis/On-board-decidim-govbr#configurando-ambiente) do decidim e seguir da parte que começa a falar em PostgressSQL.

Único adendo é fazer a parte das dependências dentro do fork do nosso repositório, que não deve ser um problema já que esse documento está dentro dele.