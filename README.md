# Sprint 01
Projeto **Monitoramento da Roçada** (nome provisório).

## Integrantes
* Felipe Souza Carvalho      - 564704
* Gustavo Hackime Costa      - 563751
* Luiz Henrique Macedo Graça - 564704
* Riquelme Santos da Mata    - 565053

---

## O Problema
As concessionárias de rodovias no estado de São Paulo possuem a obrigação contratual e de segurança de manter a vegetação (grama) das margens e canteiros centrais sob controle. O crescimento excessivo do mato obstrui a sinalização vertical, reduz a visibilidade em curvas, esconde animais que podem invadir a pista e compromete o escoamento da água da chuva. 

Atualmente, o monitoramento dessa vegetação é feito por **inspeção visual humana**, um processo:
* **Ineficiente e caro:** Exige deslocamento constante de equipes por milhares de quilômetros.
* **Reativo:** Muitas vezes o problema só é detectado quando a vegetação já ultrapassou os limites regulamentares, gerando multas pelos órgãos fiscalizadores.
* **Falho na logística:** Sem dados centralizados, o planejamento de equipes de roçagem é impreciso, gerando gastos desnecessários com deslocamentos.

## Persona
**Geraldo Souza – Gerente de Operações e Manutenção Rodoviária**
Geraldo gerencia a logística de conservação de mais de 500 km de rodovias paulistas. Ele lida diariamente com a pressão de otimizar o orçamento das equipes terceirizadas de roçagem e evitar notificações e penalidades dos órgãos reguladores. Geraldo precisa de uma ferramenta confiável que diga exatamente **onde** a intervenção é urgente, permitindo que ele envie as equipes certas para os lugares certos, sem perder tempo com vistorias manuais.

## A Proposta de Solução
A solução consiste em um sistema de monitoramento automatizado de ponta a ponta (**Edge-to-Cloud**):

1. **Hardware e Computação de Borda:** Dispositivos baseados em **ESP32-CAM** instalados estrategicamente ao longo da rodovia. Uma vez por dia, o dispositivo acorda do modo de hibernação (*Deep Sleep*), captura uma imagem da vegetação local e processa essa imagem localmente usando uma Rede Neural Convolucional (**CNN**). A inteligência classifica a altura da grama em três níveis: **Baixo (5cm)**, **Médio (15cm)** ou **Alto (>20cm)**.
2. **Rede Mesh LoRa:** Para superar a falta de cobertura de internet celular em trechos isolados de rodovias, os dispositivos comunicam-se entre si via **Rede em Malha (Mesh) utilizando tecnologia LoRa**. Os dados saltam de dispositivo em dispositivo até alcançar um Gateway em uma torre de sinal válida.
3. **Sustentabilidade Energética:** Cada módulo é autossustentável, alimentado por um mini painel solar fotovoltaico acoplado a baterias recarregáveis de Lítio, garantindo o funcionamento mesmo em períodos prolongados de chuva ou nublado.
4. **Plataforma Web (Dashboard):** Um painel web centralizado que recebe as classificações diárias e exibe o status atual de cada trecho da rodovia. O sistema ordena as prioridades de corte e fornece um histórico visual do crescimento da grama, otimizando drasticamente o planejamento logístico e as ordens de serviço da concessionária.

## Stack Tecnológica
* **Hardware:** ESP32-CAM, Módulos de rádio LoRa, Painel Solar Fotovoltaico, Baterias Li-ion.
* **Firmware & IA de Borda:** C++/C (ESP-IDF), TensorFlow Lite for Microcontrollers.
* **Protocolos de Rede:** LoRa, Protocolo de Rede Mesh Customizado.
* **Backend:** Node.js, PostgreSQL.
* **Frontend (Dashboard):** React.js, TailwindCSS.

## Justificativa de Negócio e Impacto
A implementação deste projeto transforma a manutenção de rodovias de **reativa para preditiva**. 
* **Redução de Custos Ocorrentes:** Evita o envio de viaturas de inspeção e otimiza a rota das equipes de capina, reduzindo gastos com combustível e horas-homem.
* **Mitigação de Riscos Legais:** Reduz drasticamente a chance de multas aplicadas pela ARTESP por descumprimento de metas de conservação.
* **Segurança Viária:** Garante rodovias visualmente limpas, diminuindo acidentes por falta de visibilidade ou invasão de fauna da pista.

# Requisitos e Restrições

## Requisitos funcionais

### Camada de Hardware e Firmware:
* **RF01 - Captura Diária de Imagem**: O dispositivo embarcado deve capturar uma foto da vegetação circundante exatamente uma vez a cada 24 horas.
* **RF02 - Classificação por CNN local**: O firmware deve processar a imagem internamente utilizando um modelo de rede neural convolucional e classificar a altura da vegetação em três categorias exclusivas: Baixo (5cm), Médio (15cm) ou Alto (>20cm).
* **RF03 - Transmissão Mesh**: O dispositivo deve transmitir o pacote de dados contendo ID do dispositivo e nível da vegetação para o nó vizinho mais próximo através da rede em malha LoRa.
* **RF04 - Modo de Hibernação Profunda (Deep Sleep)**: Após o processamento e envio do dado, o dispositivo deve entrar obrigatoriamente em modo de ultra-baixo consumo de energia, acordando apenas no horário agendado para a próxima captura.

### Camada de Software
* **RF05 - Painel de Status Atual**: A tela principal da dashboard deve exibir o status mais recente enviado por cada dispositivo instalado na rodovia.
* **RF06 - Ordenação por Prioridade**: A dashboard deve ordenar automaticamente os trechos da rodovia em uma lista de criticidade, posicionando os trechos com nível "Alto (>20cm)" no topo.
* **RF07 - Visualização por Mapa/Localização**: A dashboard deve plotar os pontos de monitoramento em um mapa (ou representação linear da rodovia), utilizando cores identificadoras (Verde para baixo, Amarelo para médio, Vermelho para alto).
* **RF08 - Tela de Histórico de Crescimento**: O sistema deve disponibilizar uma tela secundária que exiba gráficos de linha ou tabelas temporais demonstrando a evolução do crescimento da vegetação ao longo dos meses para cada ponto específico monitorado.
* **RF09 - Alerta de Dispositivo Inativo**: A dashboard deve gerar um alerta visual caso um dispositivo de campo fique mais de 36 horas sem enviar atualizações.

## Requisitos não funcionais
* **RNF01 - Autonomia Energética**: O conjunto bateria com painel solar deve garantir que o dispositivo de campo funcione de forma ininterrupta, suportando até 7 dias consecutivos sem incidência solar direta.
* **RNF02 - Tolerância a Falhas na Rede**: A rede LoRa Mesh deve reconfigurar suas rotas dinamicamente caso um dos nós intermediários falhe ou seja destruído, encontrando outro caminho para o dado chegar à torre de sinal.
* **RNF03 - Desempenho do Modelo**: O modelo de CNN executado no ESP32-CAM deve possuir uma acurácia mínima de 85% na classificação dos três níveis de vegetação em ambiente de testes controlados.
* **RNF04 - Usabilidade da Dashboard**: A interface da dashboard deve ser puramente expositiva, intuitiva e carregar as informações da rodovia em menos de 2 segundos após o login do usuário.
* **RNF05 - Robustez Física**: O encapsulamento do circuito físico que ficará exposto ao tempo nas margens da rodovia deve possuir proteção total contra poeira e jatos potentes de água.

## Restrições técnicas
* **RT01 - Limitação de Payload LoRa**: Como a rede LoRa opera com bandas estreitas e payloads pequenos, a imagem capturada pela câmera não pode ser transmitida pela rede. Apenas o metadado resultante, ou seja, Byte resultante e telemetrias básicas serão transmitidos.
* **RT02 - Limitação de Memória do ESP32**: O modelo de CNN desenvolvido deve sofrer processo de quantização (conversão para INT8 via TensorFlow Lite) para caber estritamente dentro da memória flash e RAM limitada do ESP32-CAM, sem estourar o buffer de execução da imagem.
* **RT03 - Janela Única de Comunicação**: Devido à hibernação (Deep Sleep) assíncrona dos dispositivos para economizar energia, a dashboard não poderá realizar requisições em tempo real para os dispositivos em campo. Ela deve atuar estritamente de forma passiva, aguardando o envio diário agendado.
* **RT04 - Dependência de Gateway de Borda**: A entrega dos dados ao servidor depende obrigatoriamente da existência de pelo menos uma "torre de sinal válida" (Gateway LoRa conectado à internet via celular) dentro do raio de alcance do último nó da malha.

# Protótipo da interface
O protótipo do design da interface pode ser acessado via link do Figma, disponível [aqui](https://www.figma.com/design/HxzdV8t6ZsWKYtTOUgi61Z/Prot%C3%B3tipo-dashboard-Challenge-Motiva?t=Fi3dbEKZ62AZfrdc-1).
