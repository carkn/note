# PlayWright の使い方
## はじめに
PlayWright とは ブラウザから操作するツール ライブラリです。  
Web オートメーションを可能とし、E2Eテストでの利用はもちろん、ヘッドレスモードで動かすことによってCIなどでも利用できます。  
参考: https://playwright.dev/
  
## 検証環境
OS: Manjaro Linux (21.2.1)  

## インストール手順
他のディストリビューションをお使いの場合も、npm インストール後は操作は同じです（多分）
```bash
# パッケージ更新
sudo pacman -Syuu

# npm のインストール
sudo pacman -S npm

# PlayWrightのインストール
npm i -D playwright

# PlayWright/test のインストール
npm install -D @playwright/test
```

## コードの生成
次にコードを自動生成します。  
```bash
npx playwright codegen https://www.google.co.jp
```
  
コードの生成はデフォルトでは Chromium が使われます。
また、テストとして操作を行っています。
1. PlayWright で https://www.google.co.jp を開く
2. 検索テキストボックスを選択する
3. "hello" と入力する
4. 検索を実行する
5. 検索結果のページからトップへ遷移する
  
以上の操作から、生成されたコードは以下の通りです。
  
```bash
# test.js
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({
    headless: false
  });
  const context = await browser.newContext();

  // Open new page
  const page = await context.newPage();

  // Go to https://www.google.co.jp/
  await page.goto('https://www.google.co.jp/');

  // Click [aria-label="検索"]
  await page.click('[aria-label="検索"]');

  // Fill [aria-label="検索"]
  await page.fill('[aria-label="検索"]', 'hello');

  // Press Enter
  await Promise.all([
    page.waitForNavigation(/*{ url: 'https://www.google.co.jp/search?q=hello&source=hp&ei=62HvYaKTM6mKr7wPod6jmAM&iflsig=ALs-wAMAAAAAYe9v-9wfjyAx08CLYm3HYmEhEh24FbxI&ved=0ahUKEwjitKP_7sv1AhUpxYsBHSHvCDMQ4dUDCAk&uact=5&oq=hello&gs_lcp=Cgdnd3Mtd2l6EAMyCwgAEIAEELEDEIMBMgUIABCABDIICAAQgAQQsQMyCAgAEIAEELEDMggIABCABBCxAzIFCAAQgAQyCAgAEIAEELEDMggIABCABBCxAzoNCAAQgAQQsQMQgwEQBDoHCAAQgAQQBDoKCAAQgAQQsQMQBFD1BFjPCWCtDWgBcAB4AIABTYgB9AKSAQE1mAEAoAEBsAEA&sclient=gws-wiz' }*/),
    page.press('[aria-label="検索"]', 'Enter')
  ]);

  // Click img[alt="Google"]
  await Promise.all([
    page.waitForNavigation(/*{ url: 'https://www.google.co.jp/webhp?hl=ja&sa=X&ved=0ahUKEwjy38aD78v1AhWIMpQKHZDkA7gQPAgI' }*/),
    page.click('img[alt="Google"]')
  ]);

  // Click .o3j99.qarstb
  await page.click('.o3j99.qarstb');

  // ---------------------
  await context.close();
  await browser.close();
})();
```

```bash
# test.ts
import { test, expect } from '@playwright/test';

test('test', async ({ page }) => {

  // Go to https://www.google.co.jp/
  await page.goto('https://www.google.co.jp/');

  // Click [aria-label="検索"]
  await page.click('[aria-label="検索"]');

  // Fill [aria-label="検索"]
  await page.fill('[aria-label="検索"]', 'hello');

  // Press Enter
  await Promise.all([
    page.waitForNavigation(/*{ url: 'https://www.google.co.jp/search?q=hello&source=hp&ei=62HvYaKTM6mKr7wPod6jmAM&iflsig=ALs-wAMAAAAAYe9v-9wfjyAx08CLYm3HYmEhEh24FbxI&ved=0ahUKEwjitKP_7sv1AhUpxYsBHSHvCDMQ4dUDCAk&uact=5&oq=hello&gs_lcp=Cgdnd3Mtd2l6EAMyCwgAEIAEELEDEIMBMgUIABCABDIICAAQgAQQsQMyCAgAEIAEELEDMggIABCABBCxAzIFCAAQgAQyCAgAEIAEELEDMggIABCABBCxAzoNCAAQgAQQsQMQgwEQBDoHCAAQgAQQBDoKCAAQgAQQsQMQBFD1BFjPCWCtDWgBcAB4AIABTYgB9AKSAQE1mAEAoAEBsAEA&sclient=gws-wiz' }*/),
    page.press('[aria-label="検索"]', 'Enter')
  ]);

  // Click img[alt="Google"]
  await Promise.all([
    page.waitForNavigation(/*{ url: 'https://www.google.co.jp/webhp?hl=ja&sa=X&ved=0ahUKEwjy38aD78v1AhWIMpQKHZDkA7gQPAgI' }*/),
    page.click('img[alt="Google"]')
  ]);

  // Click .o3j99.qarstb
  await page.click('.o3j99.qarstb');

});
```
  
## 実行してみる
```bash
node ./test.js
```

ブラウザが立ち上がりオートメーションされるのを確認できます。
また、ヘッドレスモードは以下を実行します。
  
```bash
npx playwright test test.ts
```
  
結果として以下が返ってきたら成功です。
  
```bash
test.ts:3:1 › test (6s)
```

## まとめ
この記事に書いたこと以外にも色々試してみましたが、E2E テストの自動化で使われる Selenium と遜色ないなー、むしろ、お手軽でコードも直しやすいなーと言う感触です。  
もっと深掘りしていって、いろいろなことができるようになりたいです。 