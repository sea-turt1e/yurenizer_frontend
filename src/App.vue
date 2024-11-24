// FormComponent.vue
<template>
  <!-- タイトルを表示 -->
  <h1>Yurenizer</h1>
  <h2>表記統一ツール</h2>
  <div class="form-container">
    <div class="unify-options-group">
      <h3>統一レベル</h3>
      <select 
        v-model="flgOptions.selectedUnifyLevel" 
        class="select-input"
      >
        <option value="" disabled selected>選択してください</option>
        <option 
          v-for="option in unifyLevel" 
          :key="option.value" 
          :value="option.value"
        >
          {{ option.label }}
        </option>
      </select>
    </div>

    <!-- <div class="option-heading">
      <h4>(0) 品詞</h4>
    </div>
    <div class="checkbox-group">
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isTaigen"
          class="checkbox-input"
        />
        体言
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isYougen"
          class="checkbox-input"
        />
        用言
      </label>
    </div>
    <div class="option-heading">
      <h4>(1) 語彙を統一</h4>
    </div>
    <div class="checkbox-group">
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isOtherLanguage"
          class="checkbox-input"
        />
        外国語
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isAlias"
          class="checkbox-input"
        />
        別称
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isOldName"
          class="checkbox-input"
        />
        旧称
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isMisuse"
          class="checkbox-input"
        />
        誤用
      </label>
    </div>
    <div class="option-heading">
      <h4>(2) 1の段階（語形）を統一</h4>
    </div>
    <div class="checkbox-group">
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isAlfabeticAbbreviation"
          class="checkbox-input"
        />
        アルファベットの略語
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isNonAlfabeticAbbreviation"
          class="checkbox-input"
        />
        アルファベット以外の略語
      </label>
    </div>
    <div class="option-heading">
      <h4>(3) 2の段階（略語・略称）を統一</h4>
    </div>
    <div class="checkbox-group">
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isAlphabet"
          class="checkbox-input"
        />
        アルファベット表記
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isOrthographicVariation"
          class="checkbox-input"
        />
        異表記
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isMisspelling"
          class="checkbox-input"
        />
        誤表記
      </label>
    </div> -->

    <div class="input-group">
      <p>表記を統一したいテキストを入力してください</p>
      <label for="text-input"></label>
        <textarea
          id="text-input"
          v-model="inputText"
          type="text"
          class="text-input"
          :maxlength="1000"
          placeholder="例：「東日本旅客鉄道」の表記揺れ&#10;1. JR東日本&#10;2. JR東&#10;3. JR-East"
          @focus="handleFocus"
        >
        </textarea>
    <div class="character-count" :class="{ 'is-limit': inputText.length >= 1000 }">
      {{ inputText.length }}/1000文字
    </div>
    </div>

    <button
    @click="handleSubmit"
    :disabled="isLoading || inputText.length > 1000"
    class="submit-button"
    >
    {{ isLoading ? '処理中...' : '送信' }}
    </button>

    <p v-if="error" class="error-message">{{ error }}</p>
  </div>

  <div class="result-container">
    <div class="returnText">
      <p v-if="responseMessage">{{ responseMessage }}</p>
      <p v-else></p>
    </div>
    <div class="copy-button-container" v-if="responseMessage">
      <button 
        @click="copyToClipboard" 
        class="copy-button"
      >
        {{ isCopied ? 'コピーしました!' : 'コピー' }}
      </button>
    </div>
  </div>

  
  <div class="optionExplain">
    <h3>※ツールの説明</h3>
      <p>このライブラリは、表記を統一することで文章中の表記揺れを解消します。</p>

      <h4>表記揺れの例：</h4>
      <ul>
          <li>「東日本旅客鉄道」と「JR-East」と「JR東日本」と「JR東」</li>
          <li>「アメリカ」と「アメリカ合衆国」と「United States of America」</li>
      </ul>
      <h4>表記の統一レベル設定</h4>
      <p>表記の統一方法を3段階から選択できます。対象は体言のみです。</p>
      <p>レベル1が最も広範な統一を行い、レベル3が最も限定的な統一となります。</p>
      <div class="level-explanation">
          <h5>レベル1：最も広範な統一（推奨）</h5>
            <ul>
                <li>できるだけ多くの表記を代表的な表記に統一します。</li>
                <li>対象：対訳、別称、旧称、誤用など</li>
                <li>例：「JR東日本」「JR東」「JR-East」→「東日本旅客鉄道」</li>
                <li>例：「United States of America」「アメリカ」「America」→「アメリカ合衆国」</li>
            </ul>
          <h5>レベル2：中程度の統一</h5>
          <ul>
              <li>一般的な略称のみを統一します。</li>
              <li>対象：略語、略称</li>
              <li>例：「JR東」→「JR東日本」</li>
              <li>例：「アメリカ」→「アメリカ合衆国」</li>
          </ul>
          <h5>レベル3：限定的な統一</h5>
          <ul>
              <li>最小限の表記の揺れのみを統一します。</li>
              <li>対象：略語・略称の中でアルファベット表記、異表記、誤表記</li>
              <li>例：「JR-East」→「JR東日本」</li>
              <li>例：「America」→「アメリカ」</li>
          </ul>
      </div>
    <p>※ 語彙、語形、略語・略称などは<a href="https://github.com/WorksApplications/SudachiDict/blob/develop/docs/synonyms.md" target="_blank" rel="noopener noreferrer">Sudachi同義語辞書</a>に基づいています。詳しくはそちらをご覧ください。</p>
  </div>

  <div class="others">
    <h3>その他</h3>
      <ul>
        <li>Zenn 記事: <a href="https://zenn.dev/sea_turt1e/articles/afbe326366f1e7" target="_blank" rel="noopener noreferrer">ルールベースで表記揺れを解消！Pythonライブラリ「yurenizer」</a></li>
        <li>for developers: <a href="https://github.com/sea-turt1e/yurenizer/blob/main/README_ja.md" target="_blank" rel="noopener noreferrer">GitHub</a></li>
      </ul>
  </div>
</template>

<script lang="ts">
import axios from 'axios';
import { defineComponent, reactive, ref } from 'vue';

interface Option {
  value: string
  label: string
}


export default defineComponent({
  name: 'FormComponent',
  setup() {
    const inputText = ref('「東日本旅客鉄道」の表記揺れ\n1. JR東日本\n2. JR東\n3. JR-East') 
    const isLoading = ref(false)
    const error = ref('')
    const flgOptions = reactive({
      selectedUnifyLevel: 'lexeme',
      isTaigen: true,
      isYougen: false,
      isOtherLanguage: true,
      isAlias: true,
      isOldName: true,
      isMisuse: true,
      isAlfabeticAbbreviation: true,
      isNonAlfabeticAbbreviation: true,
      isAlphabet: true,
      isOrthographicVariation: true,
      isMisspelling: true,
    })
    const responseMessage = ref('')
    const isFirstFocus = ref(true) // 初回フォーカス判定用フラグ

    const unifyLevel: Option[] = [
      { value: 'lexeme', label: '(1) 語彙' },
      { value: 'word_form', label: '(2) 語形' },
      { value: 'abbreviation', label: '(3) 略語・略称' },
    ]

    const handleSubmit = async () => {
      if (!inputText.value) {
        error.value = 'テキストを入力してください'
        return
      }

      if (!flgOptions.selectedUnifyLevel) {
        error.value = 'オプションを選択してください'
        return
      }

      try {
        isLoading.value = true
        error.value = ''

        const requestBody = {
          config:{
            unify_level: flgOptions.selectedUnifyLevel,
            taigen: flgOptions.isTaigen,
            yougen: flgOptions.isYougen,
            expansion: "from_another",
            other_language: flgOptions.isOtherLanguage,
            alias: flgOptions.isAlias,
            old_name: flgOptions.isOldName,
            misuse: flgOptions.isMisuse,
            alphabetic_abbreviation: flgOptions.isAlfabeticAbbreviation,
            non_alphabetic_abbreviation: flgOptions.isNonAlfabeticAbbreviation,
            alphabet: flgOptions.isAlphabet,
            orthographic_variation: flgOptions.isOrthographicVariation,
            misspelling: flgOptions.isMisspelling,
            }
        }
        console.log('送信直前の値:', inputText.value)
        console.log('送信直前の値:', requestBody)

        const encodedText = encodeURIComponent(inputText.value.trim())
        const response = await axios.post(
          `${import.meta.env.VITE_API_ENDPOINT}/normalize_text?text=${encodedText}`,
          requestBody,
          {
            headers: {
              accept: 'application/json',
              'Content-Type': 'application/json',
            },
          }
)


        console.log('送信成功:', response.data)
        // 成功時の処理をここに追加
        responseMessage.value = response.data.text


      } catch (e) {
        error.value = '送信に失敗しました。もう一度お試しください。'
        console.error('エラー:', e)
      } finally {
        isLoading.value = false
      }
    }
    const isCopied = ref(false)

    const copyToClipboard = async () => {
      if (!responseMessage.value) return
      
      try {
        await navigator.clipboard.writeText(responseMessage.value)
        isCopied.value = true
        setTimeout(() => {
          isCopied.value = false
        }, 2000)
      } catch (err) {
        console.error('クリップボードへのコピーに失敗しました:', err)
      }
    }

    const handleFocus = () => {
      if (isFirstFocus.value) {
        isFirstFocus.value = false
        const textarea = document.getElementById('text-input') as HTMLTextAreaElement
        textarea?.select()
      }
    }

    return {
      inputText,
      unifyLevel,
      flgOptions,
      isLoading,
      error,
      handleSubmit,
      responseMessage,
      isCopied,
      copyToClipboard,
      handleFocus,
    }
  }
})
</script>

<style scoped>
.form-container {
  /* max-width: 500px; */
  margin: 0 auto;
  padding: 20px;
}

.input-group {
  margin-bottom: 20px;
}

.input-group p {
  margin-top: 10;
  margin-bottom: 0px;
  text-align: left;
}

.select-input {
  max-width: 200%;
  padding: 0px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
  cursor: pointer;
}

.select-input:focus {
  outline: none;
  border-color: #4CAF50;
}

.unify-options-group {
  margin-bottom: 20px;
  display: flex;
  gap: 10px;
  grid-template-columns: repeat(auto-fit, minmax(50px, auto));
  justify-items: start;
}

.unify-options-group h3 {
  text-align: left;
}

.radio-item {
  display: flex;
  align-items: center;
  justify-content: center; /* 中央揃え */
  margin: 5px 0;
  padding: 5px;
}

.radio-item input[type="radio"] {
  margin-right: 8px;
}

.submit-button {
  background-color: #4CAF50;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.submit-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.error-message {
  color: red;
  margin-top: 10px;
}

.checkbox-group {
  margin: 20px 0;
  display: flex;
  gap: 20px;
}

.checkbox-label {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.checkbox-input {
  margin-right: 8px;
}

.option-heading {
  margin-top: 0px;
  margin-bottom: 0px;
  text-align: left;
}

/* 共通のコンテナ幅を設定 */
.text-input, .returnText {
  font-family: 'Arial', sans-serif; 
  font-size: 16px;
  padding: 10px;
  width: 100%;
  max-width: 800px;  /* 必要に応じて調整 */
  margin-top: 20px;
  margin-left: auto;
  margin-right: auto;
  box-sizing: border-box; /* padding を width に含める */
  min-height: 100px;
  line-height: 1.5;
  border-radius: 4px;
  text-align: left;
  resize: vertical;
}

.text-input {
  border: 1px solid #ccc;
  vertical-align: top;
  height: 120px;
}

.returnText {
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #f9f9f9;
  white-space: pre-wrap;
}

/* returnText内のpタグも調整 */
.returnText p {
  margin: 0;            /* デフォルトのマージンを削除 */
  padding: 0;           /* デフォルトのパディングを削除 */
}

.result-container {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  position: relative;
}

.copy-button-container {
  position: absolute;
  bottom: -40px;  /* ボタンの高さ + マージン */
  right: 0;
  z-index: 1;
}

.copy-button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.copy-button:hover {
  background-color: #45a049;
}

.optionExplain, .others {
  margin-top: 20px;
  padding: 10px;
  text-align: left;
}


.character-count {
  text-align: right;
  font-size: 0.8em;
  color: #666;
  margin-top: 4px;
}

.character-count.is-limit {
  color: #ff0000;
}

h1, h2, h3, h4, h5 {
  color:  #0b4eeac0;
}
</style>