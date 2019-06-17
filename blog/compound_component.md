## Tentang Compound Component

jadi apa itu compound component di React ?

Secara alih bahasa compound component berarti sebuah component yang terdiri / memiliki unsur dari beberapa component.

Tapi bukankah semua component di react juga tersusun dari component lain ?

Jadi yang di maksud compound component disini adalah bahwa component tersebut memiliki sub-sub component yang menyusun dan hanya logis ada pada component tersebut.

Ah ribet ya penjelasan pake teks. emang gampangnya pake contoh.

jadi gini.. sebelum jaman-jaman nya `Declarative UI` kita biasa bikin HTML Table dengan script seperti berikut

```html
 <table>
  <thead>
    <tr>
      <th>Month</th>
      <th>Savings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>January</td>
      <td>$100</td>
    </tr>
    <tr>
      <td>February</td>
      <td>$80</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Sum</td>
      <td>$180</td>
    </tr>
  </tfoot>
</table>
```

Simple kan ? gimana kalau kita mau nambahin caption ke table nya ?
no problem

```html
 <table>
   <caption>Monthly savings</caption>
  <thead>
    <tr>
      <th>Month</th>
      <th>Savings</th>
    </tr>
  </thead>
 ...
```

Semua berubah ketika React dan declarative UI nya menyerang.
Sekarang kita terbiasa mendeklarasikan komposisi UI melalui props semisal kita punya komponen button groups yang kurang lebih tampilanya seperti berikut [ini](https://foundation.zurb.com/sites/docs/v/5.5.3/components/button_groups.html#)
```javascript
  <ButtonGroups buttonList={['button1', 'button2', 'button3']} />
```

Well ada beberapa masalah disini. oke gimana kalau tiba tiba mas-mas desainer datang dan bilang kalau ada case kadang button nya bis di `disabled`. 

Insting `Declarative` kita mungkin akan bilang, yaudah tinggal ditambahin `props` aja kan ?. well oke.

```javascript
<ButtonGroups buttonList{['button1', 'button2', 'button3']} disabled={[2, 3]} />
```
Ini akan menambah implementasi yang cukup lumayan di dalam component `ButtonGroups` nya sendiri misal

```javascript
class ButtonGroups extends React.Component {
  // .. rest of component
  render() {
    return (
      <div className="button-container">
        {buttonList.map((buttonText, index) => {
          const isDisabled = this.props.disabled.includes(index);
          return (
            <button
              disabled={isDisabled}
              onClick={isDisabled ? null : this.onClick(index)}
            >
              {buttonText}
            </button>
          );
        })}
      </div>
    );
  }
}
```

wow, everyone is happy now. sampai kemudian datang mbak-mbak desainer yang bilang, *Well* kayanya biar colorful, warna setiap button harus bisa di customize. hmm kerjaan tambahan, but not a big deal, kita akan bikin `props` lagi.

```javascript
<ButtonGroups buttonList{['button1', 'button2', 'button3']} disabled={[2, 3]} buttonColor={['red', 'green', 'blue']} />
```

dan akan membuat implementasi kita jadi seperti berikut
```javascript
class ButtonGroups extends React.Component {
  // .. rest of component
  render() {
    return (
      <div className="button-container">
        {buttonList.map((buttonText, index) => {
          const isDisabled = this.props.disabled.includes(index);
          const className = `button button-${this.props.buttonColor[index]}`;
          return (
            <button
              disabled={isDisabled}
              className={className}
              onClick={isDisabled ? null : this.onClick(index)}
            >
              {buttonText}
            </button>
          );
        })}
      </div>
    );
  }
}
```

still easy. no big deal. lagi lagi semua happy. tapi tidak semudah itu ferguso, sampai akhir nya di meeting ada mas mas UX yang bilang `Well, kayanya ini button group nya kalau ada Icon nya akan lebih bagus untuk UX. karena orang orang kurang cukup baca text dan tanda warna. dan sepertinya suka liat yang keren keren. Kayanya lebih baik kalau ada opsi untuk meanmbah Icon di button`

Boom. kemudian kita berpikir dari props yang sudah kita buat kita mempassing text-text di button sebagai array dari string. gimana cara nambahin Icon ??? Solusi super kompleks ? mungkin kita bisa bikin custom component text yang mampu mempassing Unicode text dan me-map nya ke dalam sebuah icon dengan glyph. macem emoji-emoji gitu.
ButtonGroups kita misal akan jadi demikian
```javascript
  <ButtonGroups
    buttonList={["&#xf015; button1", "&#xf015; button2", "button3"]}
    disabled={[2, 3]}
    buttonColor={["red", "green", "blue"]}
  />
```
dan implementasi ButtonGroups nya seperti demikian
```javascript
class ButtonGroups extends React.Component {
  // .. rest of component
  render() {
    return (
      <div className="button-container">
        {buttonList.map((buttonText, index) => {
          const isDisabled = this.props.disabled.includes(index);
          const className = `button button-${this.props.buttonColor[index]}`;
          return (
            <button
              disabled={isDisabled}
              className={className}
              onClick={isDisabled ? null : this.onClick(index)}
            >
              <SomeNewTextRenderer>
              	{buttonText}
              </SomeNewTextRenderer>
            </button>
          );
        })}
      </div>
    );
  }
}
```

well tapi itu tidak ideal. karena mungkin ketimbang menggunakan unicode kita ingin pakai component Icon yang sebenernya kita sudah punya. sampai kemudian kita terpikir. `kenapa sih engga biarin aja si pengguna component ButtonGroups ini handle sendiri button nya tu mau kayak gimana`. misal kita ingin API seperti ini:
```javascript
  <ButtonGroup>
    <Button color="red" active>
      <Icon icon="home" /> Home
    </Button>
    <Button color="green" disabled>
      <Icon icon="settings" /> Settings
    </Button>
    <Button color="blue">
      <Icon icon="random" /> Random
    </Button>
  </ButtonGroup>
```
it's so cool right ? sekarang kita tidak perlu memikirkan mau isi button nya seperti apa. si component ButtonGroup  ini hanya menjadi wrapper untuk button di dalamnya.
terus component `ButtonGroup` jadi enggak guna dan mending di wrap pake div aja dong ? oh ya tentu tidak.
kita bisa menyimpan props props global yang mempengaruhi `seluruh` button di dalam ButtonGroups. jadi kita tetep mengikuti prinsip `Dont Repeat Yourself` dan enggak nulis satu satu props nya di button nya. 
Apa tu misalnya ? misal kita punya opsi untuk mengubah si ButtonGroup ini jadi rounded.
```javascript
  <ButtonGroup rounded>
    <Button color="red" active>
      <Icon icon="home" /> Home
    </Button>
    <Button color="green" disabled>
      <Icon icon="settings" /> Settings
    </Button>
    <Button color="blue">
      <Icon icon="random" /> Random
    </Button>
  </ButtonGroup>
```



implementasi ButtonGroups akan menjadi seperti ini

```javascript
class ButtonGroups extends React.Component {
  // .. rest of component
  render() {
  const isRounded = this.props.rounded;
    return (
      <div className={`button-container ${isRounded ? 'rounded' : ''}`}>
        {this.props.children}
      </div>
    );
  }
}
```

yap kita menggunakan `this.props.children` dimana itu akan merender apapun yang ada di dalam tag `<ButtonGroups> </ButtonGroups>`
misal kalau `<ButtonGroups><p>Text</p></ButtonGroups>`. 
maka `this.props.children` akan sama dengan `<p>Text</p>`

tapi kalau gitu, misal kita ingin by default ButtonGroups punya onClick Handler yang mempassing index nya seperti API diatas, gimana dong ? ini bermanfaat kalau yang ada di dalam ButtonGroups tersebut memang merupakan sebuah kesatuan opsi. sedangkan kita menggunakan `this.props.children`

ooh this is where things get cool. React memungkinkan kita untuk melooping children yang ada di dalam react dan memodifikasinya dengan gabungan utilitas `React.Children.map` dan `React.cloneElement`. misalkan dengan komponen `ButtonGroups` diatas berarti akan menjadi seperti berikut:

```javascript
class ButtonGroups extends React.Component {
  // .. rest of component
  render() {
  const isRounded = this.props.rounded;
    return (
      <div className={`button-container ${isRounded ? 'rounded' : ''}`}>
        {React.children.map(this.props.children, (child, index) => {
          return React.cloneElement(child, {
            onClick: (index) => this.props.onClick(index)
          })
        })}
      </div>
    );
  }
}
```

nah sekarang component ButtonGroups kita sudah sangat reusable. karena dia bisa memiliki onClick handler global. jadi `React.children.map` ini digunakan untuk melooping children. nah `React.cloneElement` ini memiliki dua argument. argument pertama adalah `node/component` yang ingin di `clone`. argument kedua adalah Object props yang ingin ditambahkan ke node tersebut !.

Nah jadi, itulah kurang lebih apa yang di maksud `Compound Component` pattern di react dimana intinya, Kita ingin menyerahkan bagaimana isi component tersebut di render kepada Parent Component yang menggunakan `Compound Component` tersebut.

itu dulu untuk hari ini, Happy Monday ! 
