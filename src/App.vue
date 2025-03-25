<script setup lang="ts">
import { computed, ref } from 'vue'

const pwLength = ref(12)
const excludeSimilarChars = ref(true)
const useSmallAlpha = ref(true)
const useLargeAlpha = ref(true)
const useNumber = ref(true)
const useSymbolConfig = ref<Record<string, boolean>[]>([
  {
    '!': true,
    '"': false,
    '#': true,
    $: false,
  },
  {
    '%': true,
    '&': true,
    "'": false,
    '-': true,
  },
  {
    '=': true,
    '^': false,
    '~': false,
    '@': true,
  },
  {
    ';': false,
    ':': false,
    '+': true,
    '*': false,
  },
  {
    '(': false,
    ')': false,
    '[': false,
    ']': false,
  },
  {
    '{': false,
    '}': false,
  },
])
const useSymbols = computed(() => {
  return useSymbolConfig.value.some((record) => Object.keys(record).some((key) => record[key]))
})

const SmallAplhas = Array.from({ length: 122 - 97 + 1 }, (_, index) => index + 97).map((i) =>
  String.fromCharCode(i),
)
const LargeAplhas = Array.from({ length: 90 - 65 + 1 }, (_, index) => index + 65).map((i) =>
  String.fromCharCode(i),
)
const Numbers = Array.from({ length: 57 - 48 + 1 }, (_, index) => index + 48).map((i) =>
  String.fromCharCode(i),
)
const SimilarChars = ['0', 'O', '1', 'l', 'I', 'v', 'r', 'n', '9', 'g', 'q']

const Symbols = computed(() => {
  const ret: string[] = []
  for (const recordIdx in useSymbolConfig.value) {
    const record = useSymbolConfig.value[recordIdx]
    for (const key in record) {
      const use = record[key]
      if (use) {
        ret.push(key)
      }
    }
  }
  return ret
})

const chars = computed(() => {
  let ret: string[] = []
  if (useSmallAlpha.value) {
    ret = ret.concat(SmallAplhas)
  }
  if (useLargeAlpha.value) {
    ret = ret.concat(LargeAplhas)
  }
  if (useNumber.value) {
    ret = ret.concat(Numbers)
  }
  if (useSymbols.value) {
    ret = ret.concat(Symbols.value)
  }
  if (excludeSimilarChars.value) {
    ret = ret.filter((char) => !SimilarChars.includes(char))
  }
  return ret
})
const getOneChar = () => {
  return chars.value[Math.floor(Math.random() * chars.value.length)]
}
const getCandidate = () => {
  const retArr: string[] = []
  for (let i = 0; i < pwLength.value; i++) {
    retArr.push(getOneChar())
  }
  return retArr.join('')
}
const getOnePhrase = () => {
  let count = 0
  while (true) {
    if (count++ > 10000) {
      window.alert('生成エラー')
      throw new Error('too many retries')
    }
    const cand = getCandidate()
    if (useSmallAlpha.value) {
      if (!SmallAplhas.some((char) => cand.includes(char))) {
        continue
      }
    }
    if (useLargeAlpha.value) {
      if (!LargeAplhas.some((char) => cand.includes(char))) {
        continue
      }
    }
    if (useNumber.value) {
      if (!Numbers.some((char) => cand.includes(char))) {
        continue
      }
    }
    if (useSymbols.value) {
      if (!Symbols.value.some((char) => cand.includes(char))) {
        continue
      }
    }
    return cand
  }
}

const onClickCheckAll = () => {
  useSmallAlpha.value = true
  useLargeAlpha.value = true
  useNumber.value = true
  useSymbolConfig.value.forEach((record) => {
    Object.keys(record).forEach((key) => {
      record[key] = true
    })
  })
}
const onClickUncheckAll = () => {
  useSmallAlpha.value = false
  useLargeAlpha.value = false
  useNumber.value = false
  useSymbolConfig.value.forEach((record) => {
    Object.keys(record).forEach((key) => {
      record[key] = false
    })
  })
}
</script>

<template>
  <div>
    <section class="hero is-info">
      <div class="hero-body">
        <p class="title">パスワード生成</p>
      </div>
    </section>

    <div class="container">
      <div class="content">
        <p>
          指定長のランダムなパスワードを生成します<br />
          選択した文字種は少なくとも1文字は含まれます
        </p>
      </div>

      <div class="columns is-desktop">
        <div class="column">
          <div class="field is-horizontal">
            <div class="field-label is-normal">
              <label class="label">長さ</label>
            </div>
            <div class="field-body">
              <div class="field">
                <div class="control">
                  <input class="input" type="text" v-model="pwLength" />
                </div>
              </div>
            </div>
          </div>

          <div class="field is-horizontal">
            <div class="field-label"><label class="label">類似文字</label></div>
            <div class="field-body">
              <div class="field">
                <div class="control">
                  <label class="checkbox">
                    <input type="checkbox" v-model="excludeSimilarChars" />
                    除外する
                  </label>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="column"></div>
      </div>

      <div class="columns is-desktop">
        <div class="column">
          <div class="field is-horizontal">
            <div class="field-label">
              <label class="label">使用文字</label>
            </div>
            <div class="field-body">
              <div class="field">
                <div class="control">
                  <button class="button is-small" @click="onClickCheckAll">すべてにチェック</button>
                  &nbsp;
                  <button class="button is-small" @click="onClickUncheckAll">
                    すべてのチェックを外す
                  </button>
                </div>
                <div class="control"></div>
              </div>
            </div>
          </div>
        </div>
        <div class="column"></div>
      </div>

      <div class="columns is-desktop">
        <div class="column box">
          <div class="field">
            <div class="control">
              <label class="checkbox">
                <input type="checkbox" v-model="useSmallAlpha" />
                小文字
              </label>
            </div>
          </div>
        </div>

        <div class="column box">
          <div class="field">
            <div class="control">
              <label class="checkbox">
                <input type="checkbox" v-model="useLargeAlpha" />
                大文字
              </label>
            </div>
          </div>
        </div>

        <div class="column box">
          <div class="field">
            <div class="control">
              <label class="checkbox">
                <input type="checkbox" v-model="useNumber" />
                数字
              </label>
            </div>
          </div>
        </div>
        <div class="column is-0"></div>
      </div>

      <div class="columns box is-desktop">
        <div class="column is-1"><strong>記号</strong></div>
        <template v-for="(record, idx) in useSymbolConfig" :key="idx">
          <div class="column">
            <template v-for="(val, key) in record" :key="key">
              <div class="field">
                <div class="control">
                  <label class="checkbox">
                    <input type="checkbox" v-model="record[key]" />
                    &nbsp;{{ key }}
                  </label>
                </div>
              </div>
            </template>
          </div>
        </template>
      </div>
    </div>

    <hr class="hr" />

    <div class="block">
      <div class="content">
        <h3>結果:</h3>
      </div>

      <div class="columns">
        <div class="column">
          <template v-for="n in 10" :key="n">
            <pre>{{ getOnePhrase() }}</pre>
          </template>
        </div>
        <div class="column">
          <template v-for="n in 10" :key="n">
            <pre>{{ getOnePhrase() }}</pre>
          </template>
        </div>
        <div class="column">
          <template v-for="n in 10" :key="n">
            <pre>{{ getOnePhrase() }}</pre>
          </template>
        </div>
      </div>
    </div>

    <div class="content">
      <p class="has-text-dark">{{ chars.join(', ') }}</p>
    </div>
  </div>
</template>

<style scoped>
pre {
  user-select: all;
}
</style>
