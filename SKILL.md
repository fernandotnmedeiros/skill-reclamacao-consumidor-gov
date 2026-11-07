---
name: reclamacao-consumidor-gov
description: Transforma o relato de um problema de consumo em uma reclamação estruturada, campo a campo, para o formulário "Cadastrar Reclamação" do consumidor.gov.br (empresa, contato prévio e protocolos, como comprou/contratou, área, assunto, problema, descrição e pedido). Use quando o usuário trouxer um relato de consumo e quiser registrar reclamação no consumidor.gov.br, ou pedir para "montar a reclamação", "preencher o consumidor.gov" ou verificar se a empresa participa da plataforma.
---

# Reclamação para o consumidor.gov.br

Entrada: relato livre do consumidor sobre um problema de consumo.
Saída: reclamação numerada na ordem do formulário, em texto simples, pronta para copiar e colar.

A skill NUNCA registra, envia ou submete a reclamação. Ela só prepara o conteúdo. O consumidor cola e envia pessoalmente (a plataforma exige conta gov.br prata ou ouro e a reclamação deve ser em nome próprio).

## Fluxo em duas etapas

ETAPA 1 — Conferir o relato e pedir o que falta.
ETAPA 2 — Gerar a reclamação (só quando as informações obrigatórias estiverem completas).

Não gerar a reclamação com lacunas escondidas em colchetes. Se faltar informação obrigatória, parar na Etapa 1, pedir expressamente ao usuário e aguardar a resposta. Só gerar com lacuna se o usuário disser que não sabe ou não tem a informação; nesse caso, escrever o texto sem inventar o dado e avisar na seção "Antes de enviar".

## ETAPA 1 — Informações obrigatórias e como pedir

Ler o relato e marcar o que já foi informado. Para cada item faltante, perguntar de forma direta, numerada, em uma única mensagem. Não perguntar o que já consta no relato nem o que pode ser inferido com segurança.

Informações obrigatórias:
1. Empresa: nome do fornecedor. Se houver mais de uma entidade possível (por exemplo, companhia aérea, programa de fidelidade, agência), perguntar qual prestou o serviço reclamado.
2. O que foi contratado/comprado e quando (data do contrato, da compra ou do evento).
3. Como comprou/contratou (internet, telefone, loja física etc.), se o relato não disser.
4. O que aconteceu, em ordem cronológica, com datas e horários centrais.
5. O prejuízo: valor pago, valor não devolvido, tempo perdido, despesas extras.
6. Contato prévio com a empresa (ver abaixo).
7. Pedido: o que o consumidor quer da empresa.
8. Se a reclamação é sobre uma compra ou serviço do próprio usuário (a plataforma não admite reclamação em nome de terceiros).

CONTATO PRÉVIO E PROTOCOLOS (sempre perguntar quando o relato indicar que houve contato):
O item 2.1 da saída leva APENAS o número do protocolo. Pedir expressamente o número do protocolo de CADA contato mencionado no relato. Se o relato mencionar contato mas faltar protocolo, perguntar: "Qual o número do protocolo de cada contato com a empresa? Se algum não teve protocolo, responda 'sem protocolo'." Nunca inventar ou completar protocolo.
O relato dos contatos (canal, data, o que foi dito) não vai no item 2.1: entra na descrição da reclamação, em ordem cronológica. Se faltar data ou canal de algum contato importante, perguntar junto, mas só para uso na descrição.
Se o relato disser que NÃO houve contato prévio, registrar "Não" e seguir.

Formato da mensagem de pedido (modelo):
"Para montar sua reclamação, preciso destas informações:
1. ...
2. ...
(Responda apenas o que faltar. Se não souber algum item, escreva 'não sei'.)"

## Empresa participante (verificar)

O campo "Nome da Empresa" só aceita empresas que aderiram ao consumidor.gov.br.
1. Abrir https://consumidor.gov.br/pages/principal/empresas-participantes com WebFetch. A página é grande (cerca de 186 mil caracteres): se a empresa não aparecer na primeira parte, continuar a leitura com `offset`.
2. Procurar o nome. Empresas do mesmo grupo costumam ser entradas distintas. Escolher a entrada correspondente ao serviço reclamado e usar o nome EXATAMENTE como aparece na lista.
3. Ignorar entradas "(DESATIVADA)"; se houver sucessora indicada, usar a sucessora.
4. Se a empresa não participar: avisar logo no início, indicar canais alternativos (Procon, Juizado Especial Cível, agência reguladora do setor como ANAC, ANATEL, ANS ou Banco Central) e ainda entregar descrição e pedido, que servem para outros canais. A página tem o atalho "Sugerir o cadastramento de uma nova empresa".
5. Se não for possível ler a página, avisar que a participação não foi confirmada.

## Como Comprou/Contratou

Escolher exatamente UMA opção: Catálogo | Domicílio | Ganhei de presente | Internet | Loja física | Não comprei / contratei | SMS / Mensagem de texto | Stand, feiras e eventos | Telefone.

## Área, Assunto e Problema

São listas em cascata e DEPENDEM DA EMPRESA. Escolher UM Problema, o que melhor descreve o fato central; se houver mais de um, escolher o que o consumidor mais quer resolver e citar os demais na descrição.

Ordem de consulta:
1. Empresa com lista já embutida em `references/campos-formulario.md` (transporte aéreo, Área Transportes e Assunto Aéreo; Claro Residencial, Área Telecomunicações e Assunto Pacote de Serviços): usar essa lista, copiada do formulário real, reproduzindo o texto da opção sem alterações. As listas têm data; se houver dúvida, conferir no formulário.
2. Empresa de outro segmento: consultar `references/mapa-segmentos.md` (mapa Área > Assunto > Problema lido do formulário real, uma empresa representativa por segmento, com dicionário numerado de Problemas). Identificar o segmento da empresa na lista de participantes, ler a Área e o Assunto aplicáveis e expandir o conjunto Pn pelo dicionário. Se a empresa reclamada for a própria empresa lida, usar sem ressalva; se for outra do mesmo segmento, a lista pode diferir, então escrever "(conferir no formulário)" ao lado de Área, Assunto e Problema, ou, havendo navegador e login, confirmar pela consulta ao vivo do item 3.
3. Consulta ao vivo (para confirmar ou quando o segmento não está no mapa): tentar a consulta ao vivo com o navegador do usuário já logado na conta gov.br: abrir o perfil da empresa, clicar em "Registrar Reclamação" e ler os três dropdowns (somente leitura; selecionar opções apenas para carregar a lista seguinte). NUNCA digitar credenciais, NUNCA preencher texto, NUNCA clicar em Avançar e fechar a aba ao terminar. Se o usuário não estiver logado, pedir que ele faça o login.
4. Sem navegador ou login e sem mapa aplicável: sugerir a classificação mais provável e escrever na saída "(conferir no formulário)" ao lado do item, com até 2 alternativas. Nunca apresentar como oficial uma opção que não foi lida.

## Descrição da reclamação (campo público)

Mínimo 30 e máximo de aproximadamente 3.000 caracteres. Mirar em 800 a 1.800.
- Não incluir: nome do consumidor, CPF, RG, telefone, e-mail, endereço, número de conta, senha, cartão, nem identificadores de reserva, contrato ou pedido (como localizador). Omitir e avisar. Também evitar citar nomes de municípios quando não forem necessários à compreensão do caso.
- Os protocolos NÃO vão na descrição: vão só no item 2.1. A narrativa dos contatos com a empresa (canal, data, o que foi dito) vai na descrição.
- Sem acusações, ofensas ou imputação de crime; fatos e cronologia, em primeira pessoa.
- Estrutura: (1) o que foi contratado e quando; (2) o que aconteceu, em ordem; (3) o que o consumidor fez para resolver; (4) o prejuízo, de forma objetiva.

## Pedido

Mínimo 30 e máximo de aproximadamente 1.000 caracteres. Objetivo, concreto e verificável: o que a empresa deve fazer, em que valor e em que prazo razoável (a empresa tem até 10 dias para responder). Não incluir dados pessoais.

## Anexos (opcional)

Até 5 arquivos de até 1 MB, nos formatos doc, docx, gif, jpeg, jpg, ods, odt, pdf, png, txt, xls, xlsx. Sugerir os mais úteis ao caso.

## Checagens antes de entregar

- Contar os caracteres da descrição e do pedido (com espaços).
- Revisar se há dado pessoal ou identificador de reserva nos dois textos.
- Confirmar que o nome da empresa é o da lista e que o Problema é o texto exato de uma opção real (ou marcado "conferir no formulário").
- Não afirmar direitos ou prazos legais que não tenham sido verificados.

## ETAPA 2 — Formato de saída

Texto simples, sem tabelas e sem negrito. Cada campo do formulário é um item numerado, com o rótulo exato do formulário na linha do número e o conteúdo logo abaixo, pronto para colar. Usar esta estrutura exata:

RECLAMAÇÃO PARA O CONSUMIDOR.GOV.BR
Preencha na ordem abaixo. Cada item corresponde a um campo do formulário.

1. Nome da Empresa
<nome exato da lista>

2. Procurou a empresa para solucionar o problema?
<Sim | Não>

2.1 Informe como foi o seu contato com a empresa. Indique número de protocolos, caso possua:
(somente se a resposta for Sim; colocar apenas o número ou os números, separados por vírgula; se não houve protocolo, escrever "Não possuo número de protocolo")
<protocolo 1>, <protocolo 2>

3. Como Comprou/Contratou
<opção>

4. Área
<opção>

5. Assunto
<opção>

6. Problema
<opção>

7. Descreva sua Reclamação (<N> caracteres)
<texto>

8. Descreva seu Pedido à Empresa (<N> caracteres)
<texto>

9. Anexos (opcional)
<lista curta de documentos sugeridos>

ANTES DE ENVIAR
- <no máximo 4 linhas: empresa não participante, dado omitido, item para conferir no formulário, alternativa de Problema se houver dúvida real>
- Confira os dropdowns no formulário. Esta skill não envia a reclamação.
