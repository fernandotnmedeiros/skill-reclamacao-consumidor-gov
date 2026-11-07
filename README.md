# Skill: reclamacao-consumidor-gov

Documentação da skill criada para a disciplina de Direito do Consumidor.

## 1. Objetivo

Receber o relato de um problema de consumo e devolver uma reclamação estruturada, com os campos exatos do formulário "Cadastrar Reclamação" do consumidor.gov.br, pronta para copiar e colar. A skill prepara o conteúdo; não registra nem envia a reclamação.

## 2. Estrutura de arquivos

reclamacao-consumidor-gov/
  SKILL.md                              instruções que o Claude segue (fluxo, regras, formato de saída)
  README.md                             esta documentação
  references/campos-formulario.md       listas reais do formulário (Como Comprou, Problemas completos de transporte aéreo e Claro Residencial), limites e observações
  references/mapa-segmentos.md          mapa Área > Assunto > Problema de 45 segmentos (uma empresa representativa por segmento), com dicionário numerado de 184 Problemas

## 3. Como funciona (duas etapas)

Etapa 1 — Conferência. A skill lê o relato e, se faltar informação obrigatória, pede expressamente ao usuário, em uma única mensagem numerada, e aguarda. Itens obrigatórios: empresa, o que foi contratado e quando, como comprou, cronologia, prejuízo, contato prévio, pedido e se a reclamação é do próprio usuário.

Regra do contato prévio: quando o relato indicar contato com a empresa, a skill pede o número do protocolo de cada contato. Se não houve protocolo, o usuário responde "sem protocolo". A skill nunca inventa protocolo. O item 2.1 da saída leva apenas o(s) número(s) de protocolo; o relato dos contatos (canal, data, o que foi dito) entra na descrição.

Etapa 2 — Geração. Só com as informações completas, a skill entrega a reclamação numerada. Se o usuário disser que não sabe algum dado, a skill escreve sem inventar e avisa em "Antes de enviar".

## 4. Campos produzidos (ordem e numeração da saída)

1. Nome da Empresa (verificada na lista de empresas participantes)
2. Procurou a empresa para solucionar o problema? (Sim/Não)
2.1 Número(s) de protocolo (somente se Sim)
3. Como Comprou/Contratou (uma das 9 opções)
4. Área
5. Assunto
6. Problema
7. Descreva sua Reclamação (30 a cerca de 3.000 caracteres; campo público)
8. Descreva seu Pedido à Empresa (30 a cerca de 1.000 caracteres)
9. Anexos (opcional; até 5 arquivos de 1 MB)
Fecha com a seção "Antes de enviar", de no máximo 4 linhas.

## 5. Fontes de dados

Empresa participante: página pública https://consumidor.gov.br/pages/principal/empresas-participantes (WebFetch), organizada por segmento. Há entradas "(DESATIVADA)" e entidades distintas dentro do mesmo grupo.

Como Comprou/Contratou: 9 opções fixas, iguais para todas as empresas (references/campos-formulario.md).

Área, Assunto e Problema: dependem da empresa e só aparecem no formulário com o usuário logado (conta gov.br prata ou ouro). Ordem de uso: (1) listas completas embutidas (transporte aéreo e Claro Residencial); (2) mapa por segmento (mapa-segmentos.md), lido em 07/11/2026 de uma empresa por segmento: vale integralmente para a empresa lida e como ponto de partida, a conferir, para as demais do segmento; (3) leitura ao vivo no navegador do usuário, somente leitura; (4) sugestão marcada "(conferir no formulário)".

## 6. Limites e cuidados

- Não envia reclamação, não digita credenciais, não faz login pelo usuário e não preenche o formulário. A leitura ao vivo apenas seleciona opções para carregar a lista seguinte e fecha a aba sem avançar.
- O campo de descrição é público: a skill omite nome, CPF, RG, telefone, e-mail, endereço, contas, cartões e identificadores de reserva ou contrato. Os protocolos ficam no item 2.1, não na descrição.
- A reclamação deve ser em nome do próprio consumidor.
- As listas do formulário mudam com o tempo e entre empresas. A referência embutida tem data (07/11/2026) e deve ser revalidada.
- A página tem uma divergência interna sobre a publicidade das "informações adicionais"; a skill trata a descrição como pública, por prudência.
- A skill não dá orientação jurídica individualizada e evita citar artigos de lei ou prazos sem verificação.

## 7. Manutenção

- Para incluir ou atualizar uma empresa, abrir o perfil dela logado, clicar em "Registrar Reclamação", ler os três dropdowns (sem preencher nem avançar) e acrescentar a entrada em references/mapa-segmentos.md (ou lista completa em campos-formulario.md).
- O mapa foi gerado por leitura automatizada somente leitura; os conjuntos Pn comprimem listas repetidas. Se uma lista parecer incoerente com o formulário, o formulário vale.
- Rever a referência a cada semestre ou quando o formulário mudar.
- Se os limites de caracteres mudarem, atualizar SKILL.md e a referência.

## 8. Exemplo (relato fictício)

Relato: "Comprei no site da companhia passagens de ida e volta para outubro. O voo de ida foi cancelado horas antes, sem aviso, e disseram que só me reacomodavam em dois dias, sem hospedagem. Pedi reembolso pelo chat, e depois de 20 dias nada. Comprei outra passagem e gastei com hotel."

Etapa 1 (a skill pergunta o que falta):
Para montar sua reclamação, preciso destas informações:
1. Qual a companhia aérea?
2. Qual a data do voo e do cancelamento?
3. Qual o número do protocolo do pedido de reembolso? Se não houve protocolo, responda "sem protocolo".
4. Quanto você pagou nas passagens e qual o total das despesas extras?
(Responda apenas o que faltar. Se não souber algum item, escreva "não sei".)

Etapa 2 (saída, com respostas fictícias):

RECLAMAÇÃO PARA O CONSUMIDOR.GOV.BR
Preencha na ordem abaixo. Cada item corresponde a um campo do formulário.

1. Nome da Empresa
Azul Linhas Aéreas

2. Procurou a empresa para solucionar o problema?
Sim

2.1 Informe como foi o seu contato com a empresa. Indique número de protocolos, caso possua:
AAA0000000

3. Como Comprou/Contratou
Internet

4. Área
Transportes

5. Assunto
Aéreo

6. Problema
Dificuldade / atraso na devolução de valores pagos / reembolso / retenção de valores

7. Descreva sua Reclamação (500 caracteres, aproximadamente)
Comprei pela internet passagens de ida e volta para um voo em outubro. No dia da ida, o voo foi cancelado poucas horas antes do embarque, sem aviso prévio. Fui informado de que a reacomodação só ocorreria em dois dias, sem hospedagem nem alimentação. Pedi o reembolso integral e, após 20 dias, o valor não foi devolvido. Tive prejuízo com nova passagem em outra companhia e com hospedagem.

8. Descreva seu Pedido à Empresa (200 caracteres, aproximadamente)
Solicito o reembolso integral das passagens, com correção, em até 7 dias, e a devolução das despesas comprovadas de hospedagem e da nova passagem.

9. Anexos (opcional)
Comprovante de compra; comprovante do cancelamento; comprovantes das despesas; print do protocolo.

ANTES DE ENVIAR
- Descrição e pedido foram escritos sem dados pessoais nem localizador.
- Confira os dropdowns no formulário. Esta skill não envia a reclamação.

## 9. Histórico

07/11/2026: versão inicial. Referência de transporte aéreo lida do formulário real.
07/11/2026: saída reestruturada (itens numerados, rótulos do formulário); fluxo em duas etapas; pedido expresso de protocolos e de informações faltantes.
07/11/2026: criado references/mapa-segmentos.md com Área, Assunto e Problema de uma empresa por segmento (45 segmentos), lidos do formulário real.
07/11/2026: item 2.1 passa a conter apenas o(s) número(s) de protocolo; relato dos contatos vai na descrição. Incluída a Claro Residencial na referência.
