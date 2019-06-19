Halo gaes,

hari ini saya mau sedikit ngomongin tentang test dalam software development.

Saya ga akan menjelaskan secara detail sih apa itu automated testing. tapi saya lebih akan ngobrolin tentang manfaat dan kelemahan ketika kita menuliskan automated test seperti unit test, integration test etc untuk kodingan kita.

Salah satu keuntunganya adalah kita jadi menuliskan program yang sudah di test sepenuhnya dan kita confidence kalau test yang kita tulis juga bagus makan program kita akan minim bug. 

### Keuntungan

Pertama, seperti disebutkan diatas dengan memiliki codebase yang sudah di test kita bisa yakin kalau kodingan kita bakal minim bug. karena kalau test kita memang baik dan sesuai spesifikasi dari feature nya. dan program kita lolos dari test tersebut, what could go wrong  ?

kedua, kodingan kita jadi tahan terhadap perubahan. ketika project kita menjadi semakin kompleks, biasanya kita akan melakukan refactor, atau mempersimple kodingan kita, atau memecah codebase yang tadinya `do-it-all` jadi module module independent. sebelum kemudian menambahkan fitur baru. 

Nah dalam skenario ini, kalau kita memiliki automated test itu akan sangat membantu, karena kalau kita melakukan perubahan pada codebase dan test nya masih `passing` maka berarti tidak ada yang perlu dikhawatirkan karena program masih berjalan sesuai semestinya. sebenarnya ini poin paling utama menurut saya dari menulis test. 

ketiga, kita terbiasa memikirkan logika terlebih dahulu sebelum melakukan implementasi. ini kalau kita mengimplementasi TDD ya, apa itu TDD (Test Driven Development) ? Kita bahas lain kali.

serius deh, misal kita ngebuat test untuk component React, ini salah satu test yang saya buat (versi simplifikasi nya):

```javascript
test('SelectedContact render given props', () => {
  const { container, getByText } = render(
    <SelectedContact
      avatar="http://via.placeholder.com/150x150"
      name="Nancy Carr"
      location="Manhattan"
      totalMarketPercent={11.3}
      totalItem={24}
      lastContacted="12 09 2018"
    />
  );
  expect(
    container.querySelector(`[src="http://via.placeholder.com/150x150"]`)
  ).toBeVisible();

  expect(getByText('11.3%')).toBeInTheDocument();

  expect(getByText(/24/i)).toBeInTheDocument();

  expect(getByText(/cb new york/i)).toBeInTheDocument();
  expect(getByText('12.09.2018')).toBeVisible();
  expect(getByText(/manhattan/i)).toBeInTheDocument();
  expect(getByText(/Nancy Carr/i)).toBeInTheDocument();
});

test('SelectedContact Component without agent avatar', () => {
  const { getByText } = render(
    <SelectedContact
      name="Nancy Carr"
      location="Manhattan"
      totalMarketPercent={11.3}
      totalItem={24}
      brokerage="CB New York"
      lastContacted="12.09.2018"
    />
  );

  expect(getByText(/N C/i)).toBeInTheDocument();
});
```

saya menuliskan test tersebut sebelum saya menuliskan component `SelectedContact` nya. ini akan memaksa saya berpikir terlebih dahulu, apa props yang akan dimiliki ? bagaimana component tersebut akan di render ? bagaimana kalau ada props yang tidak dimasukan (misal di komponen diatas akan merender inisial kalau avatar tidak ada) ?

semua dipikirkan bahkan sebelum ada implementasinya. tentu saja ketika kemudian menuliskan implementasinya kita sudah tidak menulis sambil berpikir logikanya seperti apa tapi logika tersebut sudah kita tuliskan di test nya

### Kekurangan

Tentu selain memiliki kelebihan TDD juga memiliki beberapa kekurangan.

pertama, menambah waktu pengerjaan. terutama kalau kita tidak biasa menulis test. sebenarnya poin ini tidak selamanya benar. ketika kita sudah terbiasa menulis test, kadang TDD justru mempercepat pekerjaan karena kita menangkap Bug sebelum bug itu muncul. tapi pada saat awal kita perlu belajar lagi bagaimana cara menulis test. dan terkadang itu memerulkan waktu untuk kemudian kita bisa ahli dalam menulis test dan sampai ke fase dimana menulis test akan mempercepat pekerjaan kita.

menurut Eric Elliot (salah satu JS Hero dan yang paling sering koar koar tentang TDD) ini butuh waktu 2 tahun [artikel dia tentang bagaimana TDD merubah hidupnya worth untuk dibaca lho](https://medium.com/javascript-scene/tdd-changed-my-life-5af0ce099f80)

kedua, efek dari nomer 1, kalau kamu freelancer atau bahkan software house kecil. kamu perlu menjelaskan ke klien tentang tambahan waktu yang dibutuhkan. dan ini bukan hal yang mudah ! seriously, kamu mau bilang ke klien mu, `emmm jadi.. kita perlu tambahan waktu sekitar 10%-20% dari waktu estimasi untuk menambahkan test ke codebase kita agar minim bug!`

ketiga, kalau kamu sebuah perusahaan, Test ini akan berguna kalau kita sudah menerapkan best practice best practice lain dalam flow developmentnya. contohnya, sudahkah menerapkan CI/CD dalam flow mu ? test yang ideal adalah yang 100% diotomasi. bukan yang mengandalkan programmer buat mandiri me-run test sebelum dia meng-commit codinganya.

Step yang ideal adalah mengintegrasikan Travis dengan Github dimana travis akan me-run automated test ketika ada Pull Request. kalau Travis/CI nya sukses. berarti codingan tersebut boleh lolos.

### Siapa sih yang sebenarnya bakal paling untung kalau nulis automated test ?

jadi setelah menuliskan kelebihan dan kekurangan seperti diatas. siapa nih yang paling diuntungkan ?

Yang paling diuntungkan adalah programmer yang mengerjakan codebase yang cukup besar dan makin nambah waktu malah jadi makin besar. apalagi kalau perbuhanya seringkali berlangsung cukup cepat.

seriously, kebanyakan programmer yang bilang test itu gak penting belum pernah mengalami harus memaintain codebase yang besar dan scale nya terus berkembang. makanya enggak heran kalau biasanya yang menyaratkan skill TDD adalah company besar seperti misal Tokopedia, Bukalapak, Gojek, etc. bayangkan sudah sebesar apa codebase mereka ?

Melakukan refactor terhadap codebase besar yang minim test itu adalah pengalaman tidak menyenangkan. kita sangat khawatir kalau perubahan yang kita lakukan terhadap codebase tersebut menghasilkan sebuah error. apalagi kalau error tersebut tidak terdeteksi QA kita dan baru muncul di production !, dan apalagi jika customer kita ratusan ribu atau bahkan jutaan yang akan terkena dampak dari bug tersebut.

saya sejujurnya sangat sangat jarang melakukan TDD di project freelance, karena 3 alasan:
1. project tersebut biasanya minta cepat
2. kebanyakan bukan program yang akan scale dalam waktu cepat
3. not worth the effort
jadi, kecuali ada spesifik request untuk automated test, saya tidak melakukanya. 

tapi justru terkadang untuk project personal saya melakukan TDD. untuk apa ? untuk proses belajar tentu saja. dengan suatu hal bellum bermanfaat untuk mu sekarang (misal kamu kerja di startup atau owner software house) bukan berarti skill tersebut tidak worth untuk dipelajari. Sedia skill berenang sebelum banjir gan.

Untuk saat ini, it's oke kalau kamu ga paham apa sih manfaat nya testing testing an itu. but do you dare to learn ?

See you again next time gan !
