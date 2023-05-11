

# privateGPT
İnternet bağlantısı olmadan belgelerinize sorular sorun ve LLM'lerin gücünden yararlanın. %100 özel, verileriniz hiçbir noktada yürütme ortamınızdan çıkmaz. Belgelerinizi bir internet bağlantısı olmadan içe aktarabilir ve sorular sorabilirsiniz!

[LangChain](https://github.com/hwchase17/langchain) ve [GPT4All](https://github.com/nomic-ai/gpt4all) ile oluşturulmuştur.

<img width="902" alt="demo" src="https://user-images.githubusercontent.com/721666/236942256-985801c9-25b9-48ef-80be-3acbb4575164.png">

# Ortam Kurulumu

Kodları burada çalıştırmak için önce tüm gereksinimleri yükleyin:

Ardından, 2 modeli indirin ve `./models` adlı bir klasöre yerleştirin:
- LLM: varsayılan olarak [ggml-gpt4all-j-v1.3-groovy.bin](https://gpt4all.io/models/ggml-gpt4all-j-v1.3-groovy.bin) kullanılır. Farklı bir GPT4All-J uyumlu model tercih ederseniz, yalnızca indirin ve `privateGPT.py` dosyasında referans verin.
- Gömme: varsayılan olarak [ggml-model-q4_0.bin](https://huggingface.co/Pi3141/alpaca-native-7B-ggml/resolve/397e872bf4c83f4c642317a5bf65ce84a105786e/ggml-model-q4_0.bin) kullanılır. Farklı bir uyumlu Gömme modeli tercih ederseniz, yalnızca indirin ve `privateGPT.py` ve `ingest.py` dosyalarında referans verin.

## Test veri kümesi
Bu depo, bir [state of the union transcript](https://github.com/imartinez/privateGPT/blob/main/source_documents/state_of_the_union.txt) örneği kullanır.

## Kendi veri kümenizi içe aktarmak için talimatlar

.Txt dosyanızı hazırlayın.

Verileri içe aktarmak için aşağıdaki komutu çalıştırın.

```shell
python ingest.py <path_to_your_txt_file>
```

Yerel vektör deposunu içeren bir `db` klasörü oluşturur. Belgenizin boyutuna bağlı olarak zaman alabilirsiniz.
`ingest` komutunu çalıştırarak istediğiniz kadar belge içe aktarabilir ve tümü yerel gömme veritabanında birikir. Sıfırdan başlamak isterseniz, `db` klasörünü silin.

Not: İçe aktarma işlemi sırasında hiçbir veri yürütme ortamınızdan çıkmaz. İnternet bağlantısı olmadan içe aktarabilirsiniz.

## Yapay zekaya sorular sorun

```shell
python privateGPT.py
```

ve kodun çalışmasını bekleyin.

```shell
> Enter a query:
```

Ardından Enter'a basın. LLM modeli prompt'u tüketip cevabı hazırlarken 20-30 saniye beklemeniz gerekebilir (makinenize bağlı olarak). Hazır olduğunda cevabı ve belgelerinizden aldığı 4 kaynağı yazdıracak; sonra başka bir soru sorabilirsiniz, betiği yeniden çalıştırmadan sadece prompt'u bekleyin.

Not: İnternet bağlantınızı kapatsanız bile betik çıkarımı hala çalışır. Veri, yerel ortamınızdan çıkmaz.

# Nasıl çalışır?

Betiğin tamamını yerel modelleri seçerek ve `LangChain`'in gücünü kullanarak, ortamınızdan herhangi bir veri çıkarmadan ve makul performansla çalıştırabilirsiniz.

- `ingest.py`, belgeyi ayrıştırmak ve `LlamaCppEmbeddings` kullanarak yerel olarak gömülü oluşturmak için `LangChain` araçlarını kullanır. Ardından, sonucu yerel bir vektör veritabanına `Chroma` vektör deposu kullanarak kaydeder.
- `privateGPT.py`, soruları anlamak ve cevaplar oluşturmak için `GPT4All-J` temelli yerel bir LLM kullanır. Cevapların bağlamı, belgelerinizden doğru bağlam parçasını bulmak için benzerlik araması kullanarak yerel vektör deposundan çıkarılır.
- `GPT4All-J` sarmalayıcısı, LangChain 0.0.162'de tanıtıldı.

# Feragatname
LLM'ler ve Vektör gömülmeleri kullanarak tamamen özel bir soru-cevap çözümü için izlenebilirliği doğrulamak için bir test projesidir. Üretim için hazır değildir ve üretimde kullanılması amaçlanmamaktadır. Modellerin seçimi, gizlilik için optimize edilmemiş, ancak gizliliği korurken performansı artırmak için farklı modeller ve vektör deposu kullanmak mümkündür.
