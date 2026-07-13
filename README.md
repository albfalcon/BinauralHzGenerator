# BinauralHzGenerator

Motivação e Origem do Projeto

Este projeto nasceu a partir de uma investigação pessoal sobre foco, concentração e estados de relaxamento, motivada pela busca de ferramentas auxiliares para lidar com desafios relacionados ao TDAH e à organização dos processos cognitivos durante atividades de estudo e criação.

A primeira versão do sistema foi desenvolvida como um experimento de geração e combinação de frequências sonoras, explorando a hipótese de que diferentes características acústicas poderiam influenciar a percepção, o estado de atenção e a experiência subjetiva durante momentos de concentração.

A ideia inicial foi criar uma ferramenta simples capaz de gerar ondas senoidais controladas, permitindo a experimentação intuitiva de variações de frequência, combinações harmônicas e diferenças entre canais de áudio. O objetivo não era criar uma solução médica ou terapêutica, mas sim construir um ambiente de exploração sonora e observação dos efeitos percebidos durante diferentes estados mentais.

Desenvolvimento experimental

A partir dessa proposta inicial, foi criado um modulador de frequência com dois osciladores independentes, permitindo:

Controle individual de frequências;
Combinação de diferentes ondas sonoras;
Experimentação de intervalos de frequência;
Separação estéreo entre canais esquerdo e direito;
Criação de padrões sonoros para momentos de estudo, concentração e relaxamento.

A construção do projeto seguiu uma abordagem experimental, unindo programação, processamento de áudio digital e observação da interação entre tecnologia e percepção humana.

Evolução da implementação

Com o desenvolvimento do sistema, surgiram necessidades técnicas relacionadas à qualidade da experiência sonora. A reprodução direta das ondas apresentava transições abruptas, levando à implementação de novos mecanismos:

Controle gradual de volume através de fade;
Gerenciamento dedicado da thread de áudio;
Controle de amplitude para evitar saturação;
Sincronização de diferenças entre frequências;
Implementação do modo binaural com controle espacial.

Essas melhorias transformaram o projeto de um simples gerador de frequências em uma plataforma experimental de síntese sonora em tempo real.

Objetivo do projeto

O objetivo principal é explorar a relação entre programação, áudio digital e percepção cognitiva, criando uma ferramenta que permita investigar como diferentes configurações sonoras podem ser utilizadas como suporte durante atividades que exigem atenção, criatividade e estados de relaxamento.

O projeto representa uma interseção entre tecnologia, neurociência cognitiva aplicada e experimentação pessoal, utilizando o desenvolvimento de software como meio de investigação e criação.


Implementação de um gerador de frequências em Java utilizando Swing para a interface gráfica e Java Sound API para síntese de áudio em tempo real. O projeto permite controlar dois osciladores independentes, ajustar frequências entre 20 Hz e 20 kHz e ativar o modo binaural por meio da diferença entre as frequências. 



Implementações e Evolução do Projeto
Visão geral

O projeto consiste em um modulador de frequência desenvolvido em Java utilizando Java Swing para interface gráfica e Java Sound API para geração de áudio em tempo real.

A primeira versão implementou a geração de duas ondas senoidais independentes com controle de frequência e reprodução estéreo. A evolução posterior introduziu melhorias de arquitetura, controle de volume, transições suaves e gerenciamento mais preciso do modo binaural.

Versão inicial
Geração de áudio em tempo real

A primeira implementação utiliza:

AudioFormat com taxa de amostragem de 44100 Hz
Áudio estéreo 16 bits
SourceDataLine para saída contínua
Thread dedicada para processamento do áudio

Cada oscilador utiliza uma onda senoidal:

Math.sin(fase)

com controle independente de frequência através dos sliders da interface.

Controle dos osciladores

Foram implementados dois osciladores independentes:

Oscilador 1
Oscilador 2

Cada um possui:

Estado ligado/desligado
Frequência própria
Controle de saída estéreo

O estado dos osciladores é compartilhado com a thread de áudio utilizando:

volatile boolean

garantindo atualização segura entre interface gráfica e processamento de áudio.

Implementações adicionadas na evolução
1. Sistema de Fade de volume
Problema encontrado

Na primeira versão, o áudio era ligado e desligado instantaneamente.

Isso poderia causar:

Estalos ("clicks") no início da reprodução
Mudanças abruptas de amplitude
Transições pouco naturais
Solução implementada

Foi criado um sistema de ganho progressivo:

Novas variáveis:

ganhoAtual1
ganhoAtual2

ganhoAlvo1
ganhoAlvo2

O volume passa a se aproximar gradualmente do valor desejado:

ganhoAtual += (ganhoAlvo - ganhoAtual) * velocidadeFade;

Benefícios:

Entrada e saída suave dos osciladores
Redução de ruídos
Melhor experiência auditiva
2. Gerenciamento aprimorado da thread de áudio
Antes

A thread permanecia dependente apenas do estado:

while (osc10n || osc20n)

Ao desligar os dois osciladores, o processamento encerrava imediatamente.

Depois

O ciclo considera também o decaimento do fade:

while (
 osc10n ||
 osc20n ||
 ganhoAtual1 > 0.001 ||
 ganhoAtual2 > 0.001
)

Isso permite que o áudio finalize naturalmente antes de encerrar a thread.

3. Controle de amplitude e prevenção de clipagem

Na versão inicial:

0.8 * Short.MAX_VALUE

era dividido apenas pelo número de osciladores ativos.

A nova implementação utiliza:

0.4 * Short.MAX_VALUE

mantendo uma margem de segurança.

Benefícios:

Menor risco de saturação digital
Melhor mistura entre frequências
Maior estabilidade sonora
4. Evolução do modo Binaural
Primeira implementação

O modo binaural utilizava uma separação simples:

Oscilador 1 → canal esquerdo
Oscilador 2 → canal direito
Nova implementação

Foi criado controle independente de ganho por canal.

Novo recurso:

ganhoDirBinaural
ganhoAlvoDirBinaural

O canal direito do Oscilador 1 recebe fade progressivo:

amostraDireita += onda1 * ganhoDirBinaural;

Isso permite uma transição suave entre:

Som estéreo normal
Modo binaural
5. Controle de diferença binaural

Foi implementado um controle de diferença entre frequências:

Exemplo:

Oscilador 1: 144 Hz
Oscilador 2: 154 Hz

Diferença binaural: 10 Hz

O sistema mantém:

diferencaBinaural = frequencia2 - frequencia1

com limite de segurança:

máximo = 55 Hz
6. Sistema de bloqueio de frequência

Nova funcionalidade:

Lock Hz

Permite manter a diferença binaural fixa.

Exemplo:

Com diferença configurada em:

10 Hz

ao alterar:

Oscilador 1: 200 Hz

o sistema automaticamente ajusta:

Oscilador 2: 210 Hz

Implementação:

frequencia2 = frequencia1 + diferencaBinaural;
Comparativo das versões
Recurso	Versão inicial	Versão atual
Dois osciladores	Sim	Sim
Onda senoidal	Sim	Sim
Controle estéreo	Sim	Melhorado
Modo binaural	Básico	Fade e controle avançado
Controle de ganho	Não	Sim
Fade de entrada/saída	Não	Sim
Proteção contra clipagem	Parcial	Melhorada
Lock de frequência	Não	Sim
Controle seguro entre threads	Básico	Melhorado
Finalização suave do áudio	Não	Sim
Arquitetura atual

Tecnologias utilizadas:

Java
Java Swing
Java Sound API
SourceDataLine
Processamento em tempo real por Thread

O projeto evoluiu de um simples gerador de frequência para um pequeno sintetizador binaural com controle dinâmico de amplitude, espacialização estéreo e sincronização inteligente de frequências.