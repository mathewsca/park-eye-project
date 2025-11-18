# Relatório Técnico – Prova de Conceito (PoC)
## Sistema de Verificação de Vagas de Estacionamento

### Introdução

Este documento apresenta os resultados de uma Prova de Conceito (PoC) para um sistema automatizado de monitoramento de vagas de estacionamento utilizando Visão Computacional e Deep Learning. O projeto visa validar a viabilidade técnica de detectar a ocupação de vagas em tempo real ou a partir de imagens estáticas.

---

### Objetivo

Validar o uso do modelo YOLOv8 para detecção de veículos e a aplicação de lógica geométrica (IoU) para determinar o status (Livre/Ocupada) de vagas previamente mapeadas, oferecendo uma solução de baixo custo e alta escalabilidade.

---

### Metodologia

#### Stack Tecnológico

- **Linguagem:** Python 3.x
- **Core de IA:** Ultralytics YOLOv8 (modelo yolov8n.pt para inferência rápida)
- **Processamento de Imagem:** OpenCV (cv2)
- **Visualização:** Matplotlib
- **Ambiente:** Jupyter Notebook
- **Persistência de Dados:** JSON (para armazenar as coordenadas das vagas)  
- **Imagens de teste de vagas:** [Parking Lot Database](https://web.inf.ufpr.br/vri/databases/parking-lot-database/) 

#### Diagrama de Arquitetura

O Diagrama ilustra visualmente o funcionamento do PoC. Ele apresenta o ciclo de vida da aplicação, desde o carregamento da imagem até a exibição do resultado final.

<img width="351" height="551" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/79ac62e0-4d83-4ada-9d51-ef5bbff4d779" />


#### Fluxo de Processamento (Pipeline)

1. **Mapeamento de Vagas (Etapa de Configuração):** 
    - Definição manual das Regiões de Interesse (ROIs) correspondentes às vagas de estacionamento.
    - As coordenadas (x1, y1, x2, y2) de cada vaga são desenhadas interativamente sobre a imagem de referência e salvas em um arquivo vagas.json. 

2. **Detecção de Objetos (Inference):**  
   - A imagem do estacionamento é processada pelo modelo YOLOv8.
   - O modelo identifica e localiza objetos das classes veiculares: Carro (id 2), Ônibus (id 5) e Caminhão (id 7).
   - O resultado é uma lista de bounding boxes (caixas delimitadoras) para cada veículo encontrado.

3. **Análise de Ocupação (Lógica Heurística):**  
   - O sistema cruza as coordenadas de cada Vaga Mapeada com as Caixas dos Veículos Detectados.
   - Utiliza-se a métrica IoU (Intersection over Union) para calcular a área de sobreposição. 
   - Critério de Ocupação: Se a sobreposição (Interseção) entre um veículo e uma vaga for superior a 20% (IOU_THRESHOLD = 0.2), o sistema classifica a vaga como "Ocupada". Caso contrário, permanece "Livre".

4. **Pós-Processamento e Visualização:**  
   - Vagas Livres: Desenhadas com contorno Verde. 
   - Vagas Ocupadas: Desenhadas com contorno Vermelho. 
   - Um painel informativo é sobreposto à imagem exibindo a contagem em tempo real (ex: "Vagas Ocupadas: 5/20").

---

### Resultados Obtidos 

**Precisão do Modelo:** O modelo YOLOv8 Nano (yolov8n.pt) demonstrou capacidade de detectar veículos, porém, em cenários de alta densidade ou baixa resolução, pode apresentar falsos negativos (carros não detectados). E ao analisar com o yolov8m.pt e yolov8s.pt, a precisão diminuiu, pois apresentou mais falsos negativos. Com isso, a precisão dos três modelos, pode ser melhor avaliada realizando retreinamentos com imagens presentes na database utilizada. Assim, o modelo pode ser ajustado para o cenário específico de estacionamento.

**Eficiência da Lógica IoU:** A lógica de sobreposição mostrou-se eficaz para associar o carro à vaga correta, tolerando pequenas variações no ângulo de estacionamento.

### Proximos Passos

- **Retreinamento do Modelo:** Utilizar imagens específicas do ambiente alvo para melhorar a precisão da detecção.

- **Otimização do Modelo:** Treinar um modelo YOLO customizado apenas com uma classe "veículo" e dataset específico de estacionamentos (visão aérea) para aumentar a acurácia.

- **Persistência Robusta:** Criar um banco de dados para histórico de ocupação e análises preditivas.
