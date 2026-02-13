İhtiyaç duyduğumuz verilerin özellikle CSV’lerde olmak üzere birçok dosyaya dağılmış olması çok yaygındır. Bazen de veri tek bir dosyadadır ama birden fazla çalışma sayfasına (worksheet) bölünmüştür. Şimdiye kadar genelde [`pandas.read_csv()`](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html) fonksiyonunu kullandık; kullanımı basittir: bir CSV verirsin, o da CSV’deki tüm sütun ve satırlarla bir DataFrame oluşturur.

Birden fazla dosya olunca iş biraz değişir. Elbette 10 dosyayı 10 farklı DataFrame’e yükleyebilirsin; peki veriyi **uzlaştırmak** / **bir araya getirmek** istersen? İşte **Pandas ile birleştirme (merge)** dünyasına hoş geldin.

## Bağlam ve Dokümantasyon

Pandas, iki DataFrame’i “bir araya getirmek” için üç temel yöntem sunar:

- [`pandas.concat()`](https://pandas.pydata.org/docs/reference/api/pandas.concat.html)
- [`pandas.DataFrame.merge()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.merge.html)
- [`pandas.DataFrame.join()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.join.html)

Hepsi dokümantasyondaki [Merge, join and concatenate](https://pandas.pydata.org/docs/user_guide/merging.html) yazısında anlatılıyor; ancak bu yazı _çok_ (gerçekten çok!) uzun. Tek seferde okuyup “tamamını anlayıp hatırlamak” pek gerçekçi değil.

## Biraz Teori

Gerçek CSV’leri yükleyip merge/join/concat denemeden önce, sözlüklerden (dictionary) üretilen küçük DataFrame’lerle çalışacağız. Böylece daha az veriyle uğraşırız ve kavramları daha rahat oturturuz.

Bu alıştırma klasöründeki `multiple_files.ipynb` not defterini aç ve ilk hücreye her zamanki gibi şu `import`’ları ekleyerek başla:

```python
import numpy as np
import pandas as pd
import matplotlib
```
Not defterine yeni bir **markdown** hücresi ekle:

```markdown
## Merge Alıştırması
```
Şimdi Google’dan derlediğimizi varsaydığımız ülke bilgilerini tutan ilk DataFrame’i oluşturalım:

```python
a_df = pd.DataFrame({
    'Country': ['Germany', 'France', 'Belgium', 'Finland'],
    'Population (M)': [82.8, 67.2, 11.4, 5.5],
    'Capital': ['Berlin', 'Paris', 'Brussels', 'Helsinki']
})
a_df
```

Yeni bir hücrede de [HDI](https://en.wikipedia.org/wiki/Human_Development_Index) tablomuz olduğunu varsayalım:

```python
b_df = pd.DataFrame({
    'Country': ['Germany', 'France', 'Belgium', 'Canada'],
    'HDI': [0.936, 0.901, 0.916, 0.926]
})
b_df
```

### İç Birleştirme
Not defterine yeni bir **markdown** hücresi ekle:

```markdown
### İç Birleştirme Alıştırması
```

`a_df` ve `b_df` DataFrame’lerini [**`merge`**](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.merge.html) ile birleştirip `null` değer içermeyen bir DataFrame elde etmeye çalış. Ortaya çıkan DataFrame’i `inner_merged_df` adlı bir değişkene ata. Fonksiyonun dokümantasyonunu özellikle `on` parametresi açısından oku. Hangi seçenek olmalı? Sadece bu argümanı verip başka hiçbir şey geçmezsen; Kanada ve Finlandiya hakkında ne söyleyebilirsin?

### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından yeni DataFrame’ini test etmek için bir kod hücresi ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('inner_merge',
    inner_merged_shape=inner_merged_df.shape,
    inner_merged_nulls=sum(inner_merged_df.isnull().sum())
)
result.write()

print(result.check())
```

Az önce **iç (inner)** birleştirme yaptık. Yani birleştirmeyi yaptığımız sütunda (`on` ile belirlenen `Column`) hem `a_df` hem de `b_df` içinde bulunan değerlerin yer aldığı satırları **yalnızca** tuttuk.

![](https://d37p7d5kaxknzw.cloudfront.net/projects/inner1.png)

Bu örnekte `a_df` içinde `Finland` satırı var ama `b_df` içinde yok; bu yüzden iç birleştirmede Finlandiya satırı yer almaz. `Canada` için de aynı durum geçerli: `b_df` içinde var ama `a_df` içinde yok; dolayısıyla iç birleştirmede o da görünmez.


#### Sol Birleştirme

Toplam dört farklı birleştirme türü var. Bir önceki bölümde _iç_ birleştirmeyi gördük. Şimdi **sol** birleştirmeyi deneyelim:

![](https://d37p7d5kaxknzw.cloudfront.net/projects/left1.png)

Not defterine yeni bir **markdown** hücresi ekle:

```markdown
### Sol Birleştirme Alıştırması
```

Yeni bir hücre oluştur ve `a_df`’yi “sol” veri kümesi kabul ederek `left_merged_df` değişkenini oluştur. Ne görüyorsun? `Finland` ne durumda? `Canada`?

### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından yeni DataFrame’ini test etmek için bir kod hücresi ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('left_merge',
    left_merged_shape=left_merged_df.shape,
    left_merged_nulls=sum(left_merged_df.isnull().sum())
)
result.write()

print(result.check())
```

### Sağ Birleştirme

Muhtemelen nereye gittiğimizi tahmin ediyorsun. Az önce _sol_ birleştirme yaptık; şimdi de **sağ** birleştirmeye bakalım!

![](https://d37p7d5kaxknzw.cloudfront.net/projects/right1.png)

Not defterine yeni bir **markdown** hücresi ekle:

```markdown
### Sağ Birleştirme Alıştırması
```

Aynı fikir: Yeni bir hücre oluştur ve `a_df` hâlâ “sol” veri kümen olacak şekilde `right_merged_df` değişkenini üret. Ne görüyorsun? `Finland`? `Canada`?

### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından yeni DataFrame’ini test etmek için bir kod hücresi ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('right_merge',
    right_merged_shape=right_merged_df.shape,
    right_merged_nulls=sum(right_merged_df.isnull().sum())
)
result.write()

print(result.check())
```


### Dış Birleştirme

Son olarak, hem `a_df` hem de `b_df` içindeki **tüm** satırları tutan birleştirme türü var: **dış (outer)** birleştirme.

![](https://d37p7d5kaxknzw.cloudfront.net/projects/outer1.png)

Not defterine yeni bir **markdown** hücresi ekle:

```markdown
### Dış Birleştirme Alıştırması
```

Ardından yeni bir hücre oluştur ve `outer_merged_df`’yi üret.

### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından yeni DataFrame’ini test etmek için bir kod hücresi ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('outer_merge',
    outer_merged_shape=outer_merged_df.shape,
    outer_merged_nulls=sum(outer_merged_df.isnull().sum())
)
result.write()

print(result.check())
```

:information_source: Pandas dokümantasyonunda [Eksik verilerle çalışma (Working with missing data)](https://pandas.pydata.org/docs/user_guide/missing_data.html) üzerine ayrı bir bölüm var. Bu da epey uzun; şu an okumak zorunda değilsin ama bir dahaki sefere `NaN`’lerle dolu bir veri seti incelerken aklında olsun.

---

### Join

`merge` fonksiyonu belirli bir **sütuna** göre birleştirme yapmak için idealdir. Şimdi de **index** (satırlar) üzerinden birleştirmek isteyeceğin bir senaryoya bakalım.

Not defterine yeni bir **markdown** hücresi ekle:

```markdown
## Join Alıştırması
```

Şimdi `Country` sütununu yeni index olacak şekilde ayarlayıp `aa_df` ve `bb_df` adında iki yeni DataFrame oluşturalım.

```python
aa_df = a_df.set_index("Country")
aa_df
```

```python
bb_df = b_df.set_index("Country")
bb_df
```

Şimdi [`pandas.DataFrame.join()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.join.html) kullanalım:

```python
aa_df.join(bb_df)
```

:question: Sonuç ne? Bu işlem sol, sağ, iç ya da dış join mi oldu?

<details><summary markdown='span'>Çözümü görüntüle
</summary>

Varsayılan olarak `.join()` **sol** join yapar. Diğer join türlerini de dene:

```python
aa_df.join(bb_df, how='inner')
aa_df.join(bb_df, how='right')
aa_df.join(bb_df, how='outer')
```

</details>

:question: Görüyorsun ki `.merge()` ve `.join()` sonunda aynı sonucu verebiliyor. Peki hangisini ne zaman kullanmalısın?

<details><summary markdown='span'>Çözümü görüntüle
</summary>

Belirli bir **sütuna** göre birleştirmek istediğinde `.merge()`; **index** üzerinden birleştirmek istediğinde `.join()` kullanabilirsin.

</details>

---

### Concat

İki DataFrame’i bir araya getirmenin üçüncü bir yolu da [`pandas.concat()`](https://pandas.pydata.org/docs/reference/api/pandas.concat.html) kullanmaktır. Hemen deneyelim:

Not defterine yeni bir **markdown** hücresi ekle:

```markdown
## Concat Alıştırması
```

```python
concat_df = pd.concat([a_df, b_df], axis="index", sort=False)
concat_df
```

Bu yöntem biraz daha “düz” çalışır: iki DataFrame’i satırlarını **üst üste ekleyerek** tek bir DataFrame’e dönüştürür. Bazı durumlarda çok işine yarayabilir; bu yüzden nasıl kullanıldığını bilmekte fayda var.

---

## Birden fazla CSV’den veri yüklemek

Şimdi öğrendiklerimizi pratiğe dökelim. Birden fazla CSV yükleme ve bunları birleştirme alıştırması için, 3 dosyadan oluşan [Olimpik Sporlar ve Madalyalar, 1896-2014](https://www.kaggle.com/the-guardian/olympic-games) veri setini kullanacağız:

- `dictionary.csv`
- `summer.csv`
- `winter.csv`

1. Bu üç dosyayı Kaggle’dan indir
2. Dosyaları mevcut klasörüne kopyala (klasör şu olmalı: `~/code/<user.github_nickname>/{{ local_path_to("02-Data-Toolkit/01-Data-Analysis/04-Multiple-Files-With-Pandas") }}`)

Çalışmayı düzenli tutmak için not defterine yeni bir **markdown** hücresi ekleyelim:

```markdown
## Olympic Sports and Medals, 1896-2014
```

Şimdi şu dosyaları yükleyecek kodu yaz:
- `dictionary.csv` dosyasını `countries_df` adlı bir DataFrame’e
- `summer.csv` dosyasını `summer_df` adlı bir DataFrame’e
- `winter.csv` dosyasını `winter_df` adlı bir DataFrame’e

`countries_df`, `summer_df` ve `winter_df` DataFrame’lerini hangi sütun üzerinden birleştirmeliyiz?


<details><summary markdown='span'>İpucu
</summary>

Winter ve summer DataFrame’lerinde [`pandas.DataFrame.rename()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rename.html) fonksiyonunu kullanman gerekecek.

</details>

### Veriyi birleştirmek

Not defterine yeni bir **markdown** hücresi ekle:

```markdown
### Verileri Birleştirme
```

Şimdi `countries_df` ile `summer_df` arasında iç birleştirme yapıp yeni bir `summer_countries_df` oluşturma zamanı. En sonunda tüm oyunları tek bir DataFrame’de birleştireceğimiz için `summer_countries_df` içine **`Season`** adlı bir sütun **ekle**.

Aynı yaklaşımı `winter_countries_df` için de uygula ve burada da mutlaka **`Season`** sütununu **ekle**.

Artık `summer_countries_df` ve `winter_countries_df` (aynı sütunlara sahipler!) DataFrame’lerini concat ederek `all_df` adlı tek bir DataFrame elde edebiliriz.

### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından yeni DataFrame’ini test etmek için bir kod hücresi ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('all_df',
    all_df_shape=all_df.shape,
    all_df_columns=set(all_df.columns)
)
result.write()

print(result.check())
```

### 1984’ten beri en iyi 10 ülke

Not defterine yeni bir **markdown** hücresi ekle:

```markdown
### En Başarılı Ülkeler Analizi
```

Boolean indexing, gruplama ve sıralama kullanarak _1984’ten beri_ en çok **toplam** madalya kazanan ilk 10 ülkeyi içeren yeni bir DataFrame oluştur.
- Sonucu `top_10_df` adlı bir değişkene kaydet
- DataFrame 10 satırdan oluşmalı ve tek bir sütunu olmalı: `Medal Count`

Sonucu çubuk grafikle çizdirmek için şunu kullanabilirsin:

```python
top_10_df.plot(kind="bar");
```


### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından DataFrame’ini test etmek için şu kodu ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('olympic_games',
    top_country_1=top_10_df.iloc[0]['Medal Count'],
    top_country_10=top_10_df.iloc[9]['Medal Count']
)
result.write()

print(result.check())
```


### 1984’ten beri en iyi 10 ülke (Sezona göre)

Analizi bir adım ileri taşıyalım: Bu kez sadece her ülkenin toplam madalya sayısını saymak istemiyoruz. Bir yandan Kış Oyunları, diğer yandan Yaz Oyunları için madalya sayılarını ayrı ayrı hesaplayacağız. Sonra da bunları çizeceğiz (sıralama yine _toplam_ madalya sayısına göre olmalı).

- DataFrame’i `top_10_season_df` adlı bir değişkene kaydet
- DataFrame 10 satır ve `Summer` `Winter` `Total` adlı 3 sütundan oluşmalı

:bulb: **İpucu 1** [`pandas.DataFrame.groupby()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.groupby.html) bir **sütun listesi** üzerinden gruplama yapabilir.

:bulb: **İpucu 2** [`pandas.DataFrame.unstack()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.unstack.html) fonksiyonunu kullanman gerekecek.

Sonucu çubuk grafikle çizdirmek için şunu kullanabilirsin:

```python
top_10_season_df[['Summer', 'Winter']].plot(kind="bar");
```

### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından DataFrame’ini test etmek için şu kodu ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('olympic_games_season',
    top_country_season_shape=top_10_season_df.shape,
    top_country_1_summer=top_10_season_df.iloc[0]['Summer'],
    top_country_10_winter=top_10_season_df.iloc[9]['Winter']
)
result.write()

print(result.check())
```


### İsteğe bağlı - 1984’ten beri etkinlik (event) bazında en iyi 10 ülke

Olimpiyatları yakından takip ediyorsan madalya sayılarının “tam doğru” görünmediğini fark etmiş olabilirsin. Not defterine yeni bir hücre ekleyip şunu çalıştır:

```python
all_df[(all_df.Year==2008) & (all_df.Event=='Basketball') & (all_df.Medal=='Gold')]
```

Görünüşe göre takım sporları analizimizde fazla sayılıyor. Takım sporları için oluşan ek satırları nasıl çıkarabiliriz?

Aynı şey karma (mixed) etkinlikler için de geçerli; bunlar iki kez sayılıyor. Onları da nasıl çıkarabilirsin?

_1984’ten beri_ toplam **event** kazanımına göre ilk 10 ülkeyi gösteren yeni bir DataFrame oluştur.

- Yeni DataFrame’i `top_10_events_df` adlı bir değişkene ata
- DataFrame 10 satır ve `Event Count` adlı 1 sütundan oluşmalı

:bulb: **İpucu 1** [`pandas.DataFrame.drop()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop.html) fonksiyonu, analiz için fazla detaylı olan sütunları kaldırmana yardımcı olabilir.

:bulb: **İpucu 2** Daha genelleştirilmiş bir veri seti için [`pandas.DataFrame.drop_duplicates()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop_duplicates.html) fonksiyonu işine yarayabilir.

Bitince sonuçları şöyle görüntüleyebilirsin:

```python
top_10_events_df.plot(kind='bar');
```

### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından DataFrame’ini test etmek için şu kodu ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('olympic_games_event',
    top_country_event_shape=top_10_events_df.shape,
    top_country_1_event=top_10_events_df.iloc[0]['Event Count'],
    top_country_10_event=top_10_events_df.iloc[9]['Event Count']
)
result.write()

print(result.check())
```

### İsteğe bağlı - Birleştirilmiş sonuçlar

`top_10_events_df` ve `top_10_df` DataFrame’lerini `top_10_combined` adlı yeni bir DataFrame’de birleştir. Birleştirme sonucunda, madalya kazanımında ilk 10’a giren **veya** event kazanımında ilk 10’a giren tüm ülkeler dahil olsun. Sonucu toplam `Medal Count` değerine göre sırala.

Grafiğini çiz
```python
top_10_combined.plot(kind='bar');
```

### Kodunu test et

Yeni bir **markdown** hücresi ekle:

```markdown
#### Kodunu kontrol et
```

ve ardından DataFrame’ini test etmek için şu kodu ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('olympic_games_combined',
    top_combined_shape=top_10_combined.shape,
    top_combined_1_event=top_10_combined.iloc[0]['Event Count'],
    top_combined_1_medal=top_10_combined.iloc[0]['Medal Count'],
    top_combined_10_event=top_10_combined.iloc[9]['Event Count'],
    top_combined_10_medal=top_10_combined.iloc[9]['Medal Count']
)
result.write()

print(result.check())
```
