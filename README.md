# 📱 CP02 — Melhorias no App Mobile (FIAP)

> Checkpoint 02 da disciplina de Mobile — aplicação de melhorias de usabilidade em um app React Native com Expo, Firebase e leitor de código de barras.

---

## 👤 Autor

| Nome | RM |
|---|---|
| Eduardo Pires Escudero | RM556527 |

---

## 📋 Sobre o Projeto

Este projeto é a continuação do app de gerenciamento de produtos desenvolvido nas aulas de Mobile da FIAP. O app permite cadastrar, editar, excluir e visualizar produtos com nome, preço e código de barras, utilizando autenticação via Firebase e leitura de código de barras pela câmera do dispositivo.

O CP02 consiste em **4 melhorias de usabilidade** aplicadas ao código original, focadas em experiência do usuário, especialmente em dispositivos com telas pequenas.

---

## ✅ Melhorias Implementadas

### 🔧 Melhoria 1 — Formatação do campo `price` no padrão brasileiro

**Problema:** O campo de preço aceitava qualquer texto sem formatação, o que tornava a entrada confusa e inconsistente com o padrão monetário brasileiro.

**Solução:** Foram criadas três funções utilitárias no `HomeScreen.js`:

- `formatPriceBR(value)` — formata os dígitos digitados em tempo real no padrão `R$ 1.234,56`, usando a abordagem de "centavos" (o número é dividido por 100 para posicionar automaticamente a vírgula).
- `parsePriceBR(formatted)` — converte o valor formatado de volta para número decimal antes de salvar no Firebase.
- `displayPrice(rawPrice)` — exibe os valores da listagem de produtos formatados com `toLocaleString("pt-BR", { style: "currency", currency: "BRL" })`.

**Resultado:** O usuário vê e digita o preço no formato `1.299,90`, e o valor é salvo corretamente como `1299.90` no banco de dados.

---

### 🔧 Melhoria 2 — Teclado dificultando a utilização em telas pequenas

**Problema:** Em dispositivos com telas menores, o teclado virtual ao aparecer cobria os campos de entrada, impedindo o usuário de ver o que estava digitando.

**Solução:** O conteúdo da tela foi envolvido em dois componentes do React Native:

- `KeyboardAvoidingView` com `behavior="padding"` (iOS) e `behavior="height"` (Android) — empurra o conteúdo para cima quando o teclado abre.
- `TouchableWithoutFeedback` com `onPress={Keyboard.dismiss}` — permite fechar o teclado ao tocar fora de qualquer campo de entrada.

**Resultado:** O teclado não sobrepõe mais os campos, e o usuário pode fechá-lo tocando em qualquer área vazia da tela.

---

### 🔧 Melhoria 3 — Preservar dados do produto ao voltar do leitor de código de barras

**Problema:** Ao abrir o leitor de código de barras no meio do preenchimento do formulário, os dados já digitados (nome, preço, modo de edição) eram perdidos ao retornar para a tela principal.

**Solução:** Foi utilizado um `useRef` (`pendingFormRef`) para armazenar o estado atual do formulário antes de navegar para a tela do scanner:

```js
// Antes de navegar
function handleOpenScanner() {
  pendingFormRef.current = { name, price, editingProductId };
  navigation.navigate("BarcodeScanner");
}

// Ao retornar com o código escaneado
useEffect(() => {
  if (route.params?.scannedBarcode) {
    if (pendingFormRef.current) {
      setName(pendingFormRef.current.name);
      setPrice(pendingFormRef.current.price);
      setEditingProductId(pendingFormRef.current.editingProductId);
      pendingFormRef.current = null;
    }
    setBarcode(String(route.params.scannedBarcode));
  }
}, [route.params?.scannedBarcode]);
```

**Resultado:** Ao voltar do scanner, nome, preço e estado de edição são restaurados automaticamente, e apenas o campo de código de barras é atualizado com o valor lido.

---

### 🔧 Melhoria 4 — Ajuste da rolagem em telas pequenas

**Problema:** Em dispositivos com telas pequenas, a lista de produtos não era rolável, fazendo com que itens no final da página ficassem inacessíveis.

**Solução:** O conteúdo da tela foi encapsulado em um `ScrollView` com as seguintes configurações:

```jsx
<ScrollView
  contentContainerStyle={{ padding: 20, paddingBottom: 40 }}
  keyboardShouldPersistTaps="handled"
  showsVerticalScrollIndicator={false}
>
```

- `keyboardShouldPersistTaps="handled"` — evita que o teclado feche ao tocar em botões dentro do scroll.
- `paddingBottom: 40` — garante que o último item da lista não fique colado na borda inferior da tela.
- `showsVerticalScrollIndicator={false}` — remove a barra de rolagem lateral para uma interface mais limpa.

**Resultado:** Todo o conteúdo da tela é acessível via scroll, mesmo nos dispositivos com menor resolução.

---

## 🗂️ Estrutura do Projeto

```
FIAP-Project-Mobile-Melhorado/
├── assets/                        # Ícones e imagens do app
├── docs/                          # Documentação das aulas (FIAP)
│   ├── aula_01_logi_navegacao.docx
│   ├── aula_02_firebase_auth.docx
│   ├── aula_03_crud.docx
│   └── aula_04_codigo_barras.docx
├── src/
│   ├── firebase/
│   │   ├── config.js              # Configuração do Firebase
│   │   ├── authService.js         # Serviços de autenticação
│   │   └── productService.js      # CRUD de produtos no Firestore
│   ├── navigation/
│   │   └── AppNavigator.js        # Configuração das rotas (Stack Navigator)
│   └── screens/
│       ├── LoginScreen.js         # Tela de login
│       ├── RegisterScreen.js      # Tela de cadastro
│       ├── ForgotPasswordScreen.js # Recuperação de senha
│       ├── HomeScreen.js          # Tela principal — CRUD + melhorias 1, 2, 3 e 4
│       └── BarcodeScannerScreen.js # Leitor de código de barras
├── App.js
├── index.js
├── app.json
└── package.json
```

---

## 🛠️ Tecnologias Utilizadas

| Tecnologia | Versão | Finalidade |
|---|---|---|
| React Native | 0.81.5 | Framework mobile |
| Expo | ~54.0.33 | Ambiente de desenvolvimento |
| Firebase | ^12.11.0 | Autenticação e banco de dados (Firestore) |
| expo-camera | ~17.0.10 | Leitor de código de barras |
| React Navigation | ^7.x | Navegação entre telas |
| React | 19.1.0 | Biblioteca base |

---

## 🚀 Como Rodar o Projeto

### Pré-requisitos

- [Node.js](https://nodejs.org/) instalado
- [Expo CLI](https://docs.expo.dev/get-started/installation/) instalado globalmente
- Aplicativo **Expo Go** no celular (iOS ou Android) ou emulador configurado

### Passos

```bash
# 1. Clone o repositório
git clone https://github.com/seu-usuario/seu-repositorio.git

# 2. Acesse a pasta do projeto
cd FIAP-Project-Mobile-Melhorado

# 3. Instale as dependências
npm install

# 4. Inicie o servidor de desenvolvimento
npm start
```

Após iniciar, escaneie o QR Code exibido no terminal com o aplicativo **Expo Go** no celular, ou pressione `a` para abrir no emulador Android, ou `i` para iOS.

> ⚠️ **Atenção:** Para que o app funcione corretamente, é necessário configurar o Firebase com suas próprias credenciais no arquivo `src/firebase/config.js`.

---

## 🔥 Configuração do Firebase

Crie um projeto no [Firebase Console](https://console.firebase.google.com/) e habilite:

- **Authentication** — método de e-mail e senha
- **Firestore Database** — banco de dados NoSQL

Em seguida, substitua o conteúdo de `src/firebase/config.js` com as credenciais do seu projeto:

```js
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";
import { getAuth } from "firebase/auth";

const firebaseConfig = {
  apiKey: "SUA_API_KEY",
  authDomain: "SEU_AUTH_DOMAIN",
  projectId: "SEU_PROJECT_ID",
  storageBucket: "SEU_STORAGE_BUCKET",
  messagingSenderId: "SEU_MESSAGING_SENDER_ID",
  appId: "SEU_APP_ID",
};

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);
export const auth = getAuth(app);
```

---

