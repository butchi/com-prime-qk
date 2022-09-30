<template>
  <v-row class="d-flex align-start mt-5">
    <v-col>
        <article>
            <h1>
                遊び方
            </h1>
            <h2 class="mt-3">
                お断り
            </h2>
            <p class="mt-3">
                本ゲームは開発中ですので、今後ゲームの内容を大きくアップデートする可能性があります。
            </p>
            <p>
                ゲーム内のデータも現状残りませんのでご注意ください。
            </p>

            <h2 class="mt-3">
                ゲームの進行
            </h2>

            <h3 class="mt-3">
                汲み斬りフェーズ
            </h3>
            <ol>
                <li>
                    ゲーム開始ボタン
                    <v-btn
                        color="primary"
                        rounded
                    >
                        <v-icon>
                            mdi-cards-playing-outline
                        </v-icon>
                    </v-btn>
                    を押します。
                </li>
                <li>
                    カードの並びが素数になるようにカードをクリックして「汲み」ボタン
                    <v-btn
                        color="accent"
                        rounded
                        plain
                    >
                        <v-icon>
                            mdi-keyboard-return
                        </v-icon>
                    </v-btn>
                    を押します。
                </li>
                <li>
                    カードを汲み切ったら「斬り」ボタン
                    <v-btn
                        color="accent"
                        rounded
                        plain
                    >
                        <v-icon>
                            mdi-arrow-right-circle
                        </v-icon>
                    </v-btn>
                    を押します。
                </li>
            </ol>

            <p>
                セットを最初からやり直すときはリセットボタン
                <v-btn
                    color="primary"
                    rounded
                    plain
                >
                    <v-icon>
                        mdi-restore
                    </v-icon>
                </v-btn>
                を押します。
            </p>

            <h3 class="mt-3">
                出会いフェーズ
            </h3>

            <p>
            </p>

            <v-card class="mt-3">
                <v-card-title>
                    完全一致
                </v-card-title>
                <v-card-text>
                    <CardLine
                        phase="result"
                        :cardNumSeq="[13, 13, 11]"
                        :cardNumSeqSucc="[13, 13, 11]"
                        :isCurrent="false"
                        :isDisable="true"
                        :isExact="true"
                        :isSuccess="true"
                        :isX3="false"
                        :isGrothen="false"
                    ></CardLine>
                    <p class="mt-3">
                        13, 13, 11をつなげた131311は素数なので完全一致
                        <v-icon
                            color="#FDD835"
                        >
                            mdi-star
                        </v-icon>
                        として成功します
                    </p>
                </v-card-text>
            </v-card>
            <v-card class="mt-3">
                <v-card-title>
                    並び替え一致
                </v-card-title>
                <v-card-text>
                    <CardLine
                        phase="result"
                        :cardNumSeq="[13, 11, 13]"
                        :cardNumSeqSucc="[13, 13, 11]"
                        :isCurrent="false"
                        :isDisable="true"
                        :isExact="false"
                        :isSuccess="true"
                        :isX3="false"
                        :isGrothen="false"
                    ></CardLine>
                    <p class="mt-3">
                        13, 11, 13は13, 13, 11と並べ替えてつなげた131311が素数なので並べ替え一致
                        <v-icon
                            color="success"
                        >
                            mdi-check
                        </v-icon>
                        として成功します
                    </p>
                </v-card-text>
            </v-card>
            <v-card class="mt-3">
                <v-card-title>
                    グロタン
                </v-card-title>
                <v-card-text>
                    <CardLine
                        phase="result"
                        :cardNumSeq="[5, 7]"
                        :cardNumSeqSucc="[5, 7]"
                        :isCurrent="false"
                        :isDisable="true"
                        :isExact="true"
                        :isSuccess="false"
                        :isX3="false"
                        :isGrothen="true"
                    ></CardLine>
                    <p class="mt-3">
                        57は素数ではありませんが、グロタンディーク素数
                        <v-icon
                            color="success"
                        >
                            mdi-alpha-g-circle
                        </v-icon>
                        として成功します
                    </p>
                </v-card-text>
            </v-card>
            <v-card class="mt-3">
                <v-card-title>
                    失敗
                </v-card-title>
                <v-card-text>
                    <CardLine
                        phase="result"
                        :cardNumSeq="[12, 12, 13]"
                        :cardNumSeqSucc="[]"
                        :isCurrent="false"
                        :isDisable="true"
                        :isExact="false"
                        :isSuccess="false"
                        :isX3="false"
                        :isGrothen="false"
                    ></CardLine>
                    <p class="mt-3">
                        12, 12, 13はどう並び替えても素数にならないため失敗
                        <v-icon
                            color="error"
                        >
                            mdi-close-circle
                        </v-icon>
                        です。
                    </p>
                </v-card-text>
            </v-card>
            <v-card class="mt-3">
                <v-card-title>
                    失敗 (3の倍数)
                </v-card-title>
                <v-card-text>
                    <CardLine
                        phase="result"
                        :cardNumSeq="[11, 12, 13]"
                        :cardNumSeqSucc="[]"
                        :isCurrent="false"
                        :isDisable="true"
                        :isExact="false"
                        :isSuccess="false"
                        :isX3="true"
                        :isGrothen="false"
                    ></CardLine>
                    <p class="mt-3">
                        11, 12, 13をつなげた111213は3の倍数で、11, 12, 13をどう並び替えても3の倍数
                        <v-icon
                            color="error"
                        >
                            mdi-numeric-3
                        </v-icon>
                        になるので失敗
                        <v-icon
                            color="error"
                        >
                            mdi-close-circle
                        </v-icon>
                        です。
                    </p>
                </v-card-text>
            </v-card>

            <p class="mt-3">
                素数判定は正確でない場合があるので、再計算ボタン
                <v-btn
                    color="warning"
                    rounded
                    plain
                >
                    <v-icon>
                        mdi-calculator
                    </v-icon>
                </v-btn>
                が表示されているときに押すと結果が改善される場合があります。
            </p>

            <p>
                出会いボタン
                <v-btn
                    color="#eebc1d"
                    rounded
                >
                    <v-icon>
                        mdi-treasure-chest
                    </v-icon>
                </v-btn>
                を押すと「出会いテム」に素数が追加されます。
            </p>

            <p>
                ゲーム開始ボタン
                <v-btn
                    color="primary"
                    rounded
                >
                    <v-icon>
                        mdi-cards-playing-outline
                    </v-icon>
                </v-btn>
                を押すと、次のセットが始まります。
            </p>
        </article>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'HowToPlayPage',
  data: () => {
    return {
    }
  },
  computed: {
  },
  mounted() {
  },
  methods: {
  },
}
</script>
