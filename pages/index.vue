<template>
  <v-row>
    <v-col md="7">
      <article>
        <v-card class="mt-3">
          <v-card-title>
            素数大富豪とは
          </v-card-title>

          <v-card-text>
            <p>素数大富豪は、2014年5月23日に当時大阪大学の大学院生だったせきゅーん氏により考案されたトランプゲームです。</p>
            <p>当初は数学愛好家を中心に広まりましたが、実は数学を苦手と感じる人でも気軽に遊べるゲームであり、現在では老若男女・数学の得意／不得意に関わらず、日本各地で親しまれています。</p>
          </v-card-text>
        </v-card>

        <v-card class="mt-3">
          <v-card-title>
            基本ルール
          </v-card-title>

          <v-card-text>
            <p>トランプを11枚ずつ配り、手札とする。プレイヤーは順番に手札を場に出していき、先に手札を出しきったプレイヤーの勝ちとなる。</p>
            <p>場に出す際は、1つの素数を作って出さなければならない。
              2枚以上を組み合わせて出す場合は、カードの数字を並べて読む
              （例: 2のカードと9のカードを組み合わせて「29」として出す）。
              また、次のプレイヤーは、前のプレイヤーと同じ枚数のカードを使って、前のプレイヤーが出した素数よりも大きい素数を出さなければならない
              （例: Qのカードと7のカードを組み合わせて「127」として出す）。</p>
            <p>素数として出した数が素数でなかった場合は、ペナルティとして手札が追加される。特殊ルールとして、グロタンディーク素数切り（57）・ラマヌジャン革命（1729）・合成数出しなどのルールもある。</p>
          </v-card-text>

          <v-card-actions>
            <v-list flat>
              <v-subheader>参考リンク</v-subheader>
              <v-list-item-group v-model="selectedItem" color="primary">
                <v-list-item href="http://integers.hatenablog.com/entry/2017/06/28/020242" target="_blank">
                  <v-list-item-icon>
                    <v-icon>mdi-open-in-new</v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    <v-list-item-title>
                      素数大富豪公式ルール（INTEGERS）
                    </v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
                <v-list-item href="https://ja.wikipedia.org/wiki/素数大富豪" target="_blank">
                  <v-list-item-icon>
                    <v-icon>mdi-open-in-new</v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    <v-list-item-title>
                      素数大富豪（Wikipedia）
                    </v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
                <v-list-item href="https://www.slideshare.net/nisei/ver2-235575554" target="_blank">
                  <v-list-item-icon>
                    <v-icon>mdi-open-in-new</v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    <v-list-item-title>
                      「素数大富豪」の遊び方（SlideShare）
                    </v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
              </v-list-item-group>
            </v-list>
          </v-card-actions>
        </v-card>
      </article>
    </v-col>
    <v-col md="5">
      <Qk></Qk>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data: () => {
    return {
      show: false,
    }
  },
}
</script>