<div align="center">

# 🛡️ PhishShield — Visual Phishing Detector

### Detecção Automatizada de Interfaces Digitais Fraudulentas com Machine Learning e Visão Computacional

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange?style=for-the-badge&logo=tensorflow&logoColor=white)](https://www.tensorflow.org/)
[![Keras](https://img.shields.io/badge/Keras-FF0000?style=for-the-badge&logo=keras&logoColor=white)](https://keras.io/)
[![Google Colab](https://img.shields.io/badge/Google%20Colab-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white)](https://colab.research.google.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

<br/>

> **Trabalho de Conclusão de Curso** — Ciência da Computação  
> Centro Universitário de João Pessoa (Unipê) · 2026  
> **Autores:** Eraldo X. P. Netto · Luiz A. S. Santino · Victor M. Q. B. Cavalcanti  
> **Orientador:** Prof. Tiago Emílio de Souza Araújo

<br/>

<img src="https://img.shields.io/badge/Acurácia%20de%20Validação-70--72%25-blueviolet?style=flat-square" />
<img src="https://img.shields.io/badge/Recall%20(Phishing)-81.8%25-critical?style=flat-square" />
<img src="https://img.shields.io/badge/AUC--ROC-0.75-informational?style=flat-square" />
<img src="https://img.shields.io/badge/Precisão-81.8%25-success?style=flat-square" />

</div>

---

## 📌 Sobre o Projeto

A maioria dos sistemas de segurança contra phishing analisa **URLs e código-fonte**. Mas e quando a URL parece normal e o código é limpo? O fraudador já ganhou — porque o usuário confia no que **vê**.

Este projeto propõe uma abordagem diferente: **analisar screenshots de interfaces digitais como objetos visuais**, usando Redes Neurais Convolucionais (CNN) com Transfer Learning para classificar páginas como **legítimas** ou **fraudulentas** com base exclusivamente em seus padrões visuais.

A abordagem foi validada em ciclos iterativos de experimentação, passando por MobileNetV2 (underfitting) → Fine-Tuning irrestrito (overfitting) → **VGG16 com Fine-Tuning seletivo (resultado final)**.

---

## 🧠 Como Funciona

```
Screenshot da Interface
        │
        ▼
┌───────────────────────────────┐
│  Pré-processamento            │
│  • Resize: 224×224 px         │
│  • Normalização de pixels     │
│  • Data Augmentation          │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│  VGG16 (Transfer Learning)    │
│  • Blocos 1–4: CONGELADOS     │  ← Conhecimento geral de imagens
│  • Bloco 5: FINE-TUNING       │  ← Adapta semântica visual de UIs
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│  Classificador Dense          │
│  • Dense(256) + ReLU          │
│  • Dropout(0.5)               │
│  • Dense(1) + Sigmoid         │
└───────────────┬───────────────┘
                │
        ┌───────┴───────┐
        ▼               ▼
   LEGÍTIMA         FRAUDULENTA
```

O módulo final integra **Grad-CAM** para explicabilidade visual + **API Gemini** para análise em linguagem natural da decisão do modelo.

---

## 📊 Resultados

### Modelo Final — VGG16 com Fine-Tuning Seletivo (15 épocas)

| Métrica | Valor |
|---|---|
| Acurácia de Treino | 84% |
| Acurácia de Validação | 70–72% |
| Precisão (Phishing) | 81,8% |
| Recall (Phishing) | **81,8%** |
| F1-Score (Phishing) | ~0.82 |
| AUC-ROC | **0.75** |

### Matriz de Confusão (220 imagens de teste)

```
                  Predito
                  Fraud.  Legít.
Verdadeiro Fraud.  [ 90 ]  [ 20 ]   ← Recall 81.8% ✅
           Legít.  [ 44 ]  [ 66 ]
```

> **90 interfaces fraudulentas corretamente bloqueadas** de 110. Apenas 20 páginas legítimas foram marcadas erroneamente como fraude.

### Evolução por Iteração

| Iteração | Arquitetura | Problema | Recall Phishing |
|---|---|---|---|
| 1ª | MobileNetV2 (congelado) + dataset desbalanceado | Underfitting / viés de classe | 44% ❌ |
| 2ª | MobileNetV2 (fine-tuning total) + 550/550 | Overfitting severo | 15–18% ❌ |
| **3ª (final)** | **VGG16 + Fine-Tuning Bloco 5** | — | **81.8% ✅** |

---

## 🗂️ Estrutura do Repositório

```
phishield/
│
├── Modelo_TCC_Final.ipynb       # Notebook principal (Google Colab)
│
├── dataset/                     # Organização esperada do dataset
│   ├── fraudulenta/             # Screenshots de páginas phishing
│   └── legitima/                # Screenshots de páginas legítimas
│
├── modelo_tcc_vgg16_final.keras # Modelo treinado (gerado pelo notebook)
│
├── assets/                      # Imagens para o README
│   ├── confusion_matrix.png
│   ├── roc_curve.png
│   └── loss_accuracy_curves.png
│
└── README.md
```

---

## 🚀 Como Executar

### Pré-requisitos

- Conta Google (para Google Drive + Colab)
- Dataset de screenshots (veja seção [Dataset](#-dataset))
- *(Opcional)* Chave de API do Google Gemini para o módulo de explicabilidade

### Passo a Passo

**1. Clone o repositório**

```bash
git clone https://github.com/seu-usuario/phishield.git
cd phishield
```

**2. Faça upload do notebook no Google Colab**

Acesse [colab.research.google.com](https://colab.research.google.com) e abra o arquivo `Modelo_TCC_Final.ipynb`.

**3. Organize o dataset no Google Drive**

```
MyDrive/
└── dataset/
    ├── fraudulenta/   # coloque aqui as screenshots fraudulentas
    └── legitima/      # coloque aqui as screenshots legítimas
```

**4. Execute as células em ordem**

| Célula | Função |
|---|---|
| 1 | Imports e configurações globais |
| 2 | Carregamento e augmentation do dataset |
| 3 | Definição da arquitetura VGG16 |
| 4 | Treinamento com EarlyStopping e checkpoint |
| 5 | Carregar modelo salvo (após reiniciar Colab) |
| 6 | Avaliação: relatório, matriz de confusão, ROC |
| 7 | Grad-CAM + análise Gemini (explicabilidade) |

**5. *(Opcional)* Módulo de explicabilidade com Gemini**

Ao executar a Célula 7, informe sua chave de API quando solicitado. O módulo gera um heatmap Grad-CAM e envia a imagem para o Gemini produzir uma explicação textual da decisão do modelo.

---

## 🗃️ Dataset

O dataset foi composto por capturas de tela de interfaces digitais, balanceado em proporção **1:1** entre as classes:

| Classe | Quantidade (treino+validação) |
|---|---|
| `fraudulenta` | 550 imagens |
| `legitima` | 550 imagens |

**Fontes utilizadas:**
- [PhishTank](https://www.phishtank.com/) — repositório colaborativo de URLs de phishing verificadas
- Capturas manuais de campanhas ativas de fraude identificadas durante a pesquisa

> ⚠️ **O dataset não está incluído neste repositório** por questões de privacidade e termos de uso das fontes. As fontes acima permitem a obtenção de dados equivalentes para reprodução dos experimentos.

---

## 🏗️ Arquitetura do Modelo em Detalhe

```python
base_model = VGG16(weights='imagenet', include_top=False, input_shape=(224, 224, 3))

# Congela blocos 1–4; libera apenas o Bloco 5
for layer in base_model.layers[:-4]:
    layer.trainable = False

model = Sequential([
    base_model,
    Flatten(),
    Dense(256, activation='relu'),
    Dropout(0.5),          # previne memorização
    Dense(1, activation='sigmoid')
])

model.compile(
    optimizer=Adam(learning_rate=0.00001),   # LR muito baixo preserva pesos VGG16
    loss='binary_crossentropy',
    metrics=['accuracy', Precision(), Recall()]
)
```

**Decisões de design:**

- **VGG16 sobre MobileNetV2:** Superior na extração de texturas estáticas, simetrias geométricas e layouts — atributos dominantes em interfaces digitais.
- **Fine-Tuning apenas no Bloco 5:** Equilibra adaptação ao domínio de UIs sem degradar os pesos genéricos de visão aprendidos no ImageNet.
- **LR = 0.00001:** Impede que a rede "esqueça" conhecimento prévio durante a adaptação.
- **Dropout 0.5 + Data Augmentation:** Combinação que foi decisiva para eliminar o overfitting da iteração anterior.

---

## 🔬 Tecnologias Utilizadas

| Tecnologia | Uso |
|---|---|
| Python 3.10+ | Linguagem principal |
| TensorFlow / Keras | Construção e treinamento do modelo |
| VGG16 (ImageNet) | Backbone para Transfer Learning |
| OpenCV (cv2) | Manipulação de imagens e Grad-CAM |
| scikit-learn | Métricas de avaliação |
| Matplotlib / Seaborn | Visualizações |
| Google Colab | Ambiente de execução (GPU gratuita) |
| Google Drive | Armazenamento de dataset e modelo |
| Google Gemini API | Explicabilidade em linguagem natural |

---

## 📚 Fundamentação Teórica

Este trabalho se apoia nos seguintes trabalhos de referência:

- **Dhamija, Tygar & Hearst (2006)** — *Why Phishing Works*: demonstrou que 23% dos usuários ignoram completamente a barra de endereços, baseando sua confiança em sinais visuais.
- **Lin et al. (2021)** — *Phishpedia*: sistema híbrido de Deep Learning focado em identidade visual de logotipos.
- **Pan & Yang (2010)** — base teórica do Transfer Learning aplicado a domínios com dados escassos.
- **LeCun, Bengio & Hinton (2015)** — fundamentos das CNNs para extração de representações visuais.
- **Ahmad et al. (2024)** — abordagem Dataset-Centric para redução de falsos positivos.

---

## 🤝 Autores

<table>
  <tr>
    <td align="center"><b>Eraldo X. P. Netto</b></td>
    <td align="center"><b>Luiz A. S. Santino</b></td>
    <td align="center"><b>Victor M. Q. B. Cavalcanti</b></td>
  </tr>
  <tr>
    <td align="center">Ciência da Computação · Unipê</td>
    <td align="center">Ciência da Computação · Unipê</td>
    <td align="center">Ciência da Computação · Unipê</td>
  </tr>
</table>

**Orientador:** Prof. Tiago Emílio de Souza Araújo — Centro Universitário de João Pessoa (Unipê)

---

## 📄 Licença

Este projeto está licenciado sob a [MIT License](LICENSE) — sinta-se livre para usar, modificar e distribuir com os devidos créditos.

---

<div align="center">

**Centro Universitário de João Pessoa — Unipê · 2026**

*"A visão computacional atua com eficiência como uma camada de segurança adaptativa de vanguarda,  
superando as limitações técnicas dos métodos tradicionais baseados em strings de URLs."*

</div>
