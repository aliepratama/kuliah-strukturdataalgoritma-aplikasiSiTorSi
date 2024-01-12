<div class="cell markdown" id="GqRsp9B2cCiE">

<img src="https://api-frontend.kemdikbud.go.id/v2/detail_pt_logo/ODcyMEY5MkMtREQyMi00RDU3LUI3MEItNTZDNzJFNUVGREMw" width="75"/>
<h1 id="judul"><b>Aplikasi SITORSI</b></h1> <h3>Laporan Tugas Besar
Struktur Data & Algoritma</h3>

![nama](https://img.shields.io/badge/Nama-Muhammad%20Ali%20Pratama%20Putra-blue)
![nim](https://img.shields.io/badge/NIM-5220411416-lightgrey)
![matkul](https://img.shields.io/badge/Mata%20Kuliah-Struktur%20Data%20&%20Algoritma-lightgrey)

------------------------------------------------------------------------

### **List of Contents**

-   <a href="#scrollTo=A7vOAVCtjWUq">Pengantar</a>

-   <a href="#scrollTo=g_XrUJnB5NhV">Pembuatan Node dan BST</a>

-   <a href="#scrollTo=g_XrUJnB5NhV">Pembuatan Objek BST dan
    Array/list</a>

-   <a href="#scrollTo=Oh2UvQYM7mZ5">Fungsi-fungsi Fitur Internal</a>

-   <a href="#scrollTo=-VQ8Hu0QJhgf">Configuration Application</a>

-   <a href="#scrollTo=R0j5v_ORKIoc">Main Menu</a>

</div>

<div class="cell markdown" id="A7vOAVCtjWUq">

### **Pengantar**

</div>

<div class="cell markdown" id="Ot5jQcjMjZ9K">

Disini saya akan memaparkan tugas besar saya di mata kuliah Struktur
Data & Algoritma. Terima kasih kepada Pak Donny Avianto, S.T., M.T.
selaku dosen pengampu mata kuliah ini. Pada tugas ini, saya mengikuti
arahan yang diberikan di soal dan menambahkan beberapa metode dan fitur
agar aplikasi dapat berjalan dengan baik. Sebagian besar kode dibawah,
terdapat banyak kode yang bersifat *customizable*. Ini bertujuan supaya
aplikasi lebih mudah di *maintainance* kedepannya. Sebagai contoh, jika
aplikasi ingin ditambah fiturnya, developer dapat membuat fungsi
tambahan dan merutekan kembali pada config variabelnya, tanpa perlu
mengubah struktur kode yang lain. Jadi aplikasi bersifat lebih modular
dan mudah untuk melakukan *debugging* jika ada kendala.

> **Projek dapat diakses pada Link Colab (Publik)** :
> <https://colab.research.google.com/drive/1n5LCsNgYtTEdxGp_eQyisFymQ0FFIxtT?usp=sharing>

Berikut Use Case diagram dari aplikasi ini:

<img src="https://i.ibb.co/4TgjpKP/Use-Case-Diagram1.jpg" width=500/>

Berikut hubungan data pada struktur data yang dibuat:

<img src="https://i.ibb.co/Ld0cgJ6/ERD.jpg" width=500/>

</div>

<div class="cell markdown" id="g_XrUJnB5NhV">

### **Pembuatan Node dan BST (Binary Search Tree)**

</div>

<div class="cell markdown" id="7gGVbJ0d5ZP4">

> Disini saya membuat node dan BST sedikit berbeda dengan yang
> diajarkan. Di kode ini saya bertujuan untuk fleksibel terhadap config
> yang diberikan. Jika kedepannya akan ada penambahan atribut pada node.
> Node akan otomatis menambah atribut sesuai dengan pengaturan yang ada
> di config.

</div>

<div class="cell markdown" id="93nyE3M8Jn4l">

1.  Class Node

    > Class ini berfungsi sebagai wadah penampungan setiap data produk
    > berisi atribut-atribut yang sesuai di config\['bst_attributes'\].
    > Untuk di contoh ini, terdapat 4 atribut yang digunakan yaitu
    > no.sku, nama produk, harga satuan, dan jumlah stok.

> Atribut dapat ditambah sesuai kebutuhan dan otomatis Node akan
> menyesuaikan dengan config yang diberikan

> Berikut diagram class Node

<img src="https://i.ibb.co/C503h5G/Main.jpg" height="150"/>

</div>

<div class="cell code" id="mkKtmHw_JN5W">

``` python
class Node:
    def __init__(self, *data):
        self.left = None
        self.right = None
        for i, v in enumerate(config['bst_attributes']):
            try:
                setattr(self, v['id'], v['type'](data[i]))
            except:
                setattr(self, v['id'], data[i])
```

</div>

<div class="cell markdown" id="IkL0iahDK_E5">

1.  Class BinarySearchTree

    > Class ini berfungsi sebagai penampungan node yang diatur tidak
    > duplikat dan terurut secara tree. Acuan pengurutan menggunakan
    > atribut no sku

> Atribut Primary/acuan dapat diubah di config. Namun data wajib bertipe
> integer

</div>

<div class="cell code" id="JCqU8Bgm3-Su">

``` python
class BinarySearchTree:
    def __init__(self):
        self.root = None
        self.total = 0

    def insert(self, *data):
        try:
            new_node = Node(*data)
        except:
            return False, 'Kesalahan Data'
        if new_node is None:
            return False, 'Kesalahan Data'
        new_node_value = vars(new_node)[config['bst_attributes'][0]['id']]
        if self.root is None:
            self.root = new_node
            self.total += 1
            return (True,)
        temp = self.root
        while True:
            if new_node_value == vars(temp)[config['bst_attributes'][0]['id']]:
                self.total += 1
                return False, config['msg_duplicate_primary_bst']
            if new_node_value < vars(temp)[config['bst_attributes'][0]['id']]:
                if temp.left is None:
                    temp.left = new_node
                    self.total += 1
                    return (True,)
                temp = temp.left
            else:
                if temp.right is None:
                    temp.right = new_node
                    self.total += 1
                    return (True,)
                temp = temp.right

    def get(self, primary_id):
        temp = self.root
        while temp is not None:
            if primary_id < vars(temp)[config['bst_attributes'][0]['id']]:
                temp = temp.left
            elif primary_id > vars(temp)[config['bst_attributes'][0]['id']]:
                temp = temp.right
            else:
                return temp
        return None

    def print_bfs(self):
        current_node = self.root
        if current_node is None:
            return None
        queue = []
        results = []
        queue.append(current_node)

        while len(queue) > 0:
            current_node = queue.pop(0)
            results.append(current_node)
            if current_node.left is not None:
                queue.append(current_node.left)
            if current_node.right is not None:
                queue.append(current_node.right)
        return results

    def print_dfs(self, order:int = 0):
        results = []
        def transverse(current_node):
            if order == 0:
                results.append(current_node)
            if current_node.left is not None:
                transverse(current_node.left)
            if order == 1:
                results.append(current_node)
            if current_node.right is not None:
                transverse(current_node.right)
            if order == 2:
                results.append(current_node)
        if self.root is None:
            return None
        transverse(self.root)
        return results

    def reconstruct(self, primary_id):
        def deleteNode(currentNode, id_key):
            if currentNode is None:
                return currentNode
            if vars(currentNode)[config['bst_attributes'][0]['id']] > id_key:
                currentNode.left = deleteNode(currentNode.left, id_key)
                return currentNode
            elif vars(currentNode)[config['bst_attributes'][0]['id']] < id_key:
                currentNode.right = deleteNode(currentNode.right, id_key)
                return currentNode
            if currentNode.left is None:
                temp = currentNode.right
                del currentNode
                return temp
            elif currentNode.right is None:
                temp = currentNode.left
                del currentNode
                return temp
            else:
                succParent = currentNode
                succ = currentNode.right
                while succ.left is not None:
                    succParent = succ
                    succ = succ.left
                if succParent != currentNode:
                    succParent.left = succ.right
                else:
                    succParent.right = succ.right
                vars(currentNode)[config['bst_attributes'][0]['id']] = vars(succ)[config['bst_attributes'][0]['id']]
                del succ
                return currentNode
        return deleteNode(self.root, primary_id)
```

</div>

<div class="cell markdown" id="jY63BDke7VKF">

### **Pembuatan Objek BST dan Array/list**

</div>

<div class="cell markdown" id="wIna6sLK7fCx">

> Tujuan kode ini untuk menampung data barang dan transaksi

</div>

<div class="cell code" id="N2GGg6vO3-Sv">

``` python
data_bst = BinarySearchTree()
data_transaksi = []
```

</div>

<div class="cell markdown" id="tnLUD_i-fwcd">

> Fungsi dibawah digunakan untuk utility dari beberapa fungsi

</div>

<div class="cell code" id="hn3HzCq4XBfV">

``` python
def expected_type(data, expected_type):
    try:
        data = expected_type(data)
    except:
        return False, data
    else:
        return True, data

def input_confirmation(message, msg_fail):
    confirm = input(f"{message} [y/n]").lower()
    while confirm not in ('y', 'n'):
        print(msg_fail)
        confirm = input(f"{message} [y/n]").lower()
    return True if confirm == 'y' else False

def input_expected_type(message, msg_fail, datatype):
    inputted = input(message)
    is_type = False
    while is_type is False:
        try:
            inputted = datatype(inputted)
        except:
            print(msg_fail)
            inputted = input(message)
        else:
            is_type = True
    return inputted

def input_next(message):
    temp = input(message)
    return True
```

</div>

<div class="cell markdown" id="NfIRQipzf4tY">

> Fungsi pembuat tabel

</div>

<div class="cell code" id="5v4t4BCC9eBz">

``` python
def make_table(title_column:list, multi_list:list):
    width = list(map(lambda x: x[1], title_column))
    print('-' * (sum(width) + len(width)))
    for i, v in enumerate(title_column):
        if i == len(title_column) - 1:
            print(f"{str(v[0][:width[i]]).ljust(width[i])}", end="\n")
        else:
            print(f"{str(v[0][:width[i]]).ljust(width[i])}", end=" ")
    print('-' * (sum(width) + len(width)))
    for j in multi_list:
        for i, v in enumerate(j):
            if i == len(title_column) - 1:
                print(f"{str(v)[:width[i]].ljust(width[i])}", end="\n")
            else:
                print(f"{str(v)[:width[i]].ljust(width[i])}", end=" ")
```

</div>

<div class="cell markdown" id="Oh2UvQYM7mZ5">

### **Fungsi-Fungsi Fitur Internal**

</div>

<div class="cell markdown" id="saoHUKA2E_DJ">

1.  Tambah data produk

> Fungsi ini bertujuan untuk menambah data-data produk seperti: No.SKU,
> Nama Produk, Harga Satuan, dan Jumlah stok pada BST. Kode ini terlihat
> panjang karena menyesuaikan pengaturan dari config, melakukan input
> konfirmasi ulang, dan banyak penanganan error dari input user.

</div>

<div class="cell code" id="vleZbatCFTZe">

``` python
def a1():
    global data_bst
    print(config['menu'][0]['lists'][0])
    data = []
    for i in config['bst_attributes']:
        data.append(input(f"Masukkan data {i['title']} -> "))
    print(f"{config['menu'][0]['lists'][0]}\nKonfirmasi Data")
    confirm = input_confirmation(config['msg_confirmation'], config['msg_not_in_choise'])
    if confirm:
        if len(data[0]) <= 4:
            status_process = data_bst.insert(*data)
            if status_process[0]:
                print('Penambahan Data Berhasil ...')
                return True
            print(status_process[1])
        else:
            print('No SKU tidak boleh melebihi 4 digit angka')
    print('Mohon ulangi lagi')
    return False
```

</div>

<div class="cell markdown" id="uV4z6VQ8zXga">

1.  Restok produk

> Fungsi ini bertujuan untuk update tambah stok data yang ada di BST

</div>

<div class="cell code" id="PevvvU3YFVi9">

``` python
def a2():
    print(config['menu'][0]['lists'][1])
    inputted = input('Masukkan no sku -> ')
    while inputted.isnumeric() == False or len(inputted) > 4:
        print('no sku tidak sesuai')
        inputted = input('Masukkan no sku -> ')
    node_target = data_bst.get(int(inputted))
    if node_target is not None:
        print(f"No. SKU -> {inputted}")
        old_stock = vars(node_target)[config['bst_stock_attribute']]
        print(f"Total stok sekarang -> {old_stock}")
        addition = input("Stok yang ingin ditambah -> ")
        while inputted.isnumeric() == False:
            print('Stok tidak valid')
            addition = input("Stok yang ingin ditambah -> ")
        print(f"Total yang akan disimpan -> {old_stock + int(addition)}")
        confirm = input_confirmation(config['msg_confirmation'], config['msg_not_in_choise'])
        if confirm:
            vars(node_target)[config['bst_stock_attribute']] += int(addition)
            print('Penambahan Stok Berhasil ...')
            return True
        else:
            print('Mohon ulangi lagi')
    else:
        print('no sku tidak terdaftar mohon buat terlebih dahulu')
    return False
```

</div>

<div class="cell markdown" id="4cBUI3Bm2jtr">

1.  Tampil Stok berdasarkan No. SKU

> Fungsi ini bertujuan untuk menampilkan data stok produk berdasarkan no
> sku yang diberikan

</div>

<div class="cell code" id="M3VipzOO2lRJ">

``` python
def a3():
    print(config['menu'][0]['lists'][2])
    inputted = input('Masukkan no sku -> ')
    while inputted.isnumeric() == False or len(inputted) > 4:
        print('no sku tidak sesuai')
        inputted = input('Masukkan no sku -> ')
    node_target = data_bst.get(int(inputted))
    if node_target is not None:
        stock = vars(node_target)[config['bst_stock_attribute']]
        print(f'Stok dengan no sku {inputted} adalah {stock}')
        input_next('Ketik apapun untuk melanjutkan ...')
        return True
    else:
        print('no sku tidak terdaftar mohon buat terlebih dahulu')
        return False
```

</div>

<div class="cell markdown" id="z-zrMGVE3TnQ">

1.  Lihat Semua Barang

> Fungsi ini bertujuan untuk menampilkan seluruh data produk yang ada di
> BST

</div>

<div class="cell code" id="QrSf0dcO4MP4">

``` python
def a4():
    print(config['menu'][0]['lists'][3])
    print_method = ('Breadth First Search', 'DFS Preorder', 'DFS Inorder', 'DFS Postorder')
    for i, v in enumerate(print_method):
        print(f'[{i}] {v}')
    inputted = input('Masukkan metode cetak -> ')
    while inputted.isnumeric() == False or expected_type(inputted, int)[1] >= len(print_method) or expected_type(inputted, int)[1] < 0:
        print('Pilihan tidak valid')
        inputted = input('Masukkan metode cetak -> ')
    column = (('No. SKU', 7), ('Nama Barang', 15), ('Jumlah Stok', 12), ('Harga Satuan', 12))
    if inputted == '0':
        data_node = data_bst.print_bfs()
    elif inputted == '1':
        data_node = data_bst.print_dfs(0)
    elif inputted == '2':
        data_node = data_bst.print_dfs(1)
    elif inputted == '3':
        data_node = data_bst.print_dfs(2)
    if data_node is not None:
        data = list(map(lambda x: [str(x.sku).zfill(4), str(x.nama_barang), str(x.harga_satuan), str(x.jumlah_stok)], data_node))
        make_table(column, data)
    else:
        print('Belum ada data apapun')
    input_next('Ketik apapun untuk melanjutkan ...')
    return True
```

</div>

<div class="cell markdown" id="aczrqmYy3miD">

1.  Hapus Barang

> Dengan memakai method reconstruct(), user dapat menghapus produk
> dengan aman pada BST

</div>

<div class="cell code" id="1ITtC76tDWXt">

``` python
def a5():
    print(config['menu'][0]['lists'][4])
    inputted = input('Masukkan no sku -> ')
    while inputted.isnumeric() == False or len(inputted) > 4:
        print('no sku tidak sesuai')
        inputted = input('Masukkan no sku -> ')
    node_target = data_bst.get(int(inputted))
    if node_target is not None:
        data_bst.reconstruct(int(inputted))
        return True
    else:
        print('no sku tidak terdaftar mohon buat terlebih dahulu')
        return False
```

</div>

<div class="cell markdown" id="XPbSNy4PYmpQ">

1.  Tambah Transaksi

> Fungsi ini bertujuan menambah data transaksi pada list

</div>

<div class="cell code" id="YhHR1ZB3FXZV">

``` python
def b1():
    temp_data = []
    print(config['menu'][1]['lists'][0])
    for i, v in enumerate(config['transaction_data']):
        if v['is_shown']:
            inputted = input(f"Masukkan data {v['title']} -> ")
            if v['is_int']:
                while inputted.isnumeric() == False:
                    print('data tidak valid')
                    inputted = input(f"Masukkan data {v['title']} -> ")
            if v['rule'] is None:
                temp_data.append(inputted)
            else:
                value_rule = v['rule'](inputted, temp_data)
                if value_rule != False:
                    temp_data.append(value_rule)
                else:
                    return False
        else:
            value_rule = v['rule'](temp_data)
            if value_rule != False:
                temp_data.append(value_rule)
            else:
                return False
    if len(temp_data) == len(config['transaction_data']):
        confirm = input_confirmation(config['msg_confirmation'], config['msg_not_in_choise'])
        if confirm:
            data_transaksi.append(temp_data)
            config['transaction_data_rule'](temp_data)
            print('Penambahan Transaksi Berhasil ...')
            return True
        print('Mohon isi data kembali ...')
        return False
```

</div>

<div class="cell markdown" id="GYJFCsAnJRjn">

> Fungsi-fungsi dibawah mendukung fitur diatas berupa aturan-aturan yang
> memvalidasi dan manipulasi input menjadi data yang diinginkan. Kinerja
> fungsi ini mirip seperti call and response pada suatu aplikasi.

</div>

<div class="cell code" id="0uq5QGn13-Sx">

``` python
def column2(sku, data):
    sku = int(sku)
    node_target = data_bst.get(sku)
    while node_target is None:
        confirm = input_confirmation("no sku tidak terdaftar. Masukkan lagi?", config['msg_not_in_choise'])
        if confirm is False:
            return False
        sku = input('Masukkan kembali no SKU')
        while sku.isnumeric() == False:
            print('Stok tidak valid')
            sku = input('Masukkan kembali no SKU')
        sku = int(sku)
        node_target = data_bst.get(sku)
    return sku

def column3(stok, data):
    stok = int(stok)
    node_target = data_bst.get(data[1])
    while stok > node_target.jumlah_stok:
        confirm = input_confirmation('stok tidak tersedia. Masukkan lagi? [y/n]', config['msg_not_in_choise'])
        if confirm is False:
            return False
        stok = input('Masukkan kembali stok')
        while stok.isnumeric() == False:
            print('Stok tidak valid')
            stok = input('Masukkan kembali stok')
        stok = int(stok)
    return stok

def column4(data):
    node_target = data_bst.get(data[1])
    return node_target.harga_satuan * data[2]

def data_list_handler(data):
    node_target = data_bst.get(data[1])
    node_target.jumlah_stok -= data[2]
    return True
```

</div>

<div class="cell markdown" id="ysE3wkchI2Xc">

1.  Tampil data transaksi

> Fungsi ini bertujuan menampilkan data transaksi yang ada di list

</div>

<div class="cell code" id="OTiaDQy4K_rQ">

``` python
def show_data_transaksi(data:list):
    column = (('Nama Konsumen', 15), ('No. SKU', 7), ('Jumlah Beli', 11), ('Subtotal', 12))
    make_table(column, data)
```

</div>

<div class="cell code" id="JLoZw9BoFczC">

``` python
def b2():
    print(config['menu'][1]['lists'][1])
    if len(data_transaksi) > 0:
        show_data_transaksi(data_transaksi)
        input_next('Ketik apapun untuk melanjutkan ...')
        return True
    else:
        print('Tidak ada data Transaksi')
        return False
```

</div>

<div class="cell markdown" id="6-N_WHKmI_Us">

1.  Tampil data transaksi terurut

> Fungsi ini bertujuan menampilkan data transaksi yang ada di list
> dengan terurut berdasarkan subtotal. Pada kasus ini menggunakan
> algoritma bubblesort

</div>

<div class="cell code" id="BAb7UJOh3-Sv">

``` python
def b3():
    print(config['menu'][1]['lists'][2])
    if len(data_transaksi) > 0:
        view_list = data_transaksi
        show_data_transaksi(config['sort_method'](view_list, 3))
        input_next('Ketik apapun untuk melanjutkan ...')
        return True
    else:
        print('Tidak ada data Transaksi')
        return False
```

</div>

<div class="cell markdown" id="93kFXZ1PKQLe">

1.  Edit Data Transaksi

> Fungsi ini dapat mengubah data transaksi berdasarkan id yang diberikan

</div>

<div class="cell code" id="Hmi02qBBKnow">

``` python
def b4():
    print(config['menu'][1]['lists'][3])
    if len(data_transaksi) > 0:
        column = (('id', 4), ('Nama Konsumen', 15), ('No. SKU', 7), ('Jumlah Beli', 11), ('Subtotal', 12))
        data = list(map(lambda x: (x[0], *x[1]), list(enumerate(data_transaksi))))
        make_table(column, data)
        index_transaksi = input('Masukkan index data yang ingin di edit -> ')
        while index_transaksi.isnumeric() == False or expected_type(index_transaksi, int)[1] >= len(data_transaksi) or expected_type(index_transaksi, int)[1] < 0:
            print('Pilihan tidak valid')
            index_transaksi = input('Masukkan index data yang ingin di edit -> ')
        temp_data = []
        for i, v in enumerate(config['transaction_data']):
            if v['is_shown']:
                inputted = input(f"Masukkan data {v['title']} -> ")
                if v['is_int']:
                    while inputted.isnumeric() == False:
                        print('data tidak valid')
                        inputted = input(f"Masukkan data {v['title']} -> ")
                if v['rule'] is None:
                    temp_data.append(inputted)
                else:
                    value_rule = v['rule'](inputted, temp_data)
                    if value_rule != False:
                        temp_data.append(value_rule)
                    else:
                        return False
            else:
                value_rule = v['rule'](temp_data)
                if value_rule != False:
                    temp_data.append(value_rule)
                else:
                    return False
        if len(temp_data) == len(config['transaction_data']):
            confirm = input_confirmation(config['msg_confirmation'], config['msg_not_in_choise'])
            if confirm:
                data_transaksi[int(index_transaksi)] = temp_data
                config['transaction_data_rule'](temp_data)
                print('Edit Transaksi Berhasil ...')
                return True
            return False
    else:
        print('Tidak ada data Transaksi')
        return False
```

</div>

<div class="cell markdown" id="9pJVeU0EKWTy">

1.  Hapus Data Transaksi

> Fungsi ini berguna untuk menghapus data transaksi berdasarkan id.

</div>

<div class="cell code" id="fCYRBApzKqW4">

``` python
def b5():
    print(config['menu'][1]['lists'][4])
    if len(data_transaksi) > 0:
        column = (('id', 4), ('Nama Konsumen', 12), ('No. SKU', 5), ('Jumlah Beli', 11), ('Subtotal', 12))
        data = list(map(lambda x: (x[0], *x[1]), list(enumerate(data_transaksi))))
        make_table(column, data)
        inputted = input('Masukkan index data yang ingin di hapus -> ')
        while inputted.isnumeric() == False or expected_type(inputted, int)[1] >= len(data_transaksi) or expected_type(inputted, int)[1] < 0:
            print('Pilihan tidak valid')
            inputted = input('Masukkan index data yang ingin di hapus -> ')
        status = data_transaksi.pop(int(inputted))
        if status is not None:
            print('Hapus Transaksi Berhasil ...')
            return True
        else:
            print('Hapus Transaksi Gagal ...')
            return False
    else:
        print('Tidak ada data Transaksi')
        return False
```

</div>

<div class="cell markdown" id="3dIX95PYJdsB">

> Fungsi-fungsi dibawah mendukung fitur diatas

</div>

<div class="cell code" id="ZdxpaZ-p3-Sw">

``` python
def bubbleSort(data, anchor_index):
    for i in range(len(data) - 1, 0, -1):
        for j in range(i):
            if data[j][anchor_index] > data[j+1][anchor_index]:
                data[j], data[j+1] = data[j+1], data[j]
    return data
```

</div>

<div class="cell markdown" id="-VQ8Hu0QJhgf">

## ### **Configuration Application**

</div>

<div class="cell markdown" id="dZrypRiCJwt-">

> Kode ini membuat aplikasi menjadi lebih mudah untuk dimaintenance dan
> mudah untuk melakukan penambahan fitur

</div>

<div class="cell code" id="xZhVTHTs3-Sy">

``` python
config = {
    'title': 'Selamat Datang di Aplikasi SITORSI',
    'menu' : [
        {
            'title': 'Kelola Stok Barang',
            'lists': [
                'Input Data Stok Barang',
                'Restok Barang',
                'Lihat Stok berdasarkan SKU',
                'Lihat Semua Barang',
                'Hapus Data Barang',
            ],
            'routes': [a1, a2, a3, a4, a5]
        },
        {
            'title': 'Kelola Transaksi Konsumen',
            'lists': [
                'Input Data Transaksi Baru',
                'Lihat Data Seluruh Transaksi Konsumen',
                'Lihat Data Transaksi Berdasarkan Subtotal',
                'Edit Data Transaksi',
                'Hapus Data Transaksi',
            ],
            'routes': [b1, b2, b3, b4, b5]
        }
    ],
    'bst_attributes': [
        # Index ke-0 akan menjadi Primary Attribute
        {
            'id': 'sku',
            'title': 'Nomor SKU',
            'is_int': True,
            'type': int,
        },
        {
            'id': 'nama_barang',
            'title': 'Nama Barang',
            'is_int': False,
            'type': str,
        },
        {
            'id': 'harga_satuan',
            'title': 'Harga Satuan',
            'is_int': True,
            'type': int,
        },
        {
            'id': 'jumlah_stok',
            'title': 'Jumlah Stok',
            'is_int': True,
            'type': int,
        },
    ],
    'transaction_data': [
        {
            'id': 'nama_konsumen',
            'title': 'Nama Konsumen',
            'is_shown': True,
            'is_int': False,
            'type': str,
            'rule': None
        },
        {
            'id': 'sku',
            'title': 'No SKU yang dibeli',
            'is_shown': True,
            'is_int': True,
            'type': int,
            'rule': column2
        },
        {
            'id': 'jumlah_beli',
            'title': 'Jumlah Beli',
            'is_shown': True,
            'is_int': True,
            'type': int,
            'rule': column3
        },
        {
            'id': 'subtotal',
            'title': 'Subtotal',
            'is_shown': False,
            'is_int': True,
            'type': int,
            'rule': column4
        }
    ],
    'bst_primary_attribute': 'sku',
    'bst_stock_attribute': 'jumlah_stok',
    'transaction_data_primary': 'subtotal',
    'transaction_data_rule': data_list_handler,
    'view_method': make_table,
    'WIDTH_VIEW': 60,
    'sort_method': bubbleSort,
    'back_choise': 'Kembali',
    'exit_choise': 'Keluar Aplikasi',
    'msg_choise': 'Silahkan masukkan pilihan anda -> ',
    'msg_not_in_choise': 'Mohon isikan pilihan dengan benar...',
    'msg_confirmation': 'Apakah data yang diatas sudah benar?',
    'msg_duplicate_primary_bst': 'Terdapat Kesamaan No SKU',
    'msg_exit': 'Terima kasih',
    'delay_input': 1,
    'delay_after_program': 2,
    'delay_msg': 2
}
```

</div>

<div class="cell markdown" id="epmUr0vFJ8Pi">

> Berikut adalah masing-masing kegunaan keys

</div>

<div class="cell markdown" id="BdQXij99Y5m0">

| Key                       | Kegunaan                                                           | Tipe Data |
|---------------------------|--------------------------------------------------------------------|-----------|
| title                     | Menampilkan judul pada main menu                                   | String    |
| menu                      | Membuat Struktur menu (max. 1 lapisan sub menu)                    | List      |
| bst_attributes            | Membuat atribut node yang dibutuhkan BST                           | List      |
| transaction_data          | Membuat kolom-kolom pada list transaksi                            | List      |
| bst_primary_attribute     | Menyesuaikan atribut acuan sesuai id                               | String    |
| bst_stock_attribute       | Menyesuaikan atribut stok sesuai id                                | String    |
| transaction_data_primary  | Menyesuaikan kolom acuan sesuai id                                 | String    |
| transaction_data_rule     | Menjalankan fungsi ini setelah melaksanakan append pada list utama | Function  |
| view_method               | Jenis metode penampilan output yang dipilih                        | Function  |
| WIDTH_VIEW                | Panjang layar output                                               | Integer   |
| sort_method               | Metode algoritma sorting yang dipilih                              | Function  |
| back_choise               | Tampilan pilihan kembali                                           | String    |
| exit_choise               | Tampilan pilihan keluar                                            | String    |
| msg_choise                | Pesan untuk memilih menu                                           | String    |
| msg_not_in_choise         | Pesan error input di luar pilihan                                  | String    |
| msg_confirmation          | Pesan konfirmasi                                                   | String    |
| msg_duplicate_primary_bst | Pesan error duplikat no sku                                        | String    |
| msg_exit                  | Pesan setelah keluar aplikasi                                      | String    |
| delay_input               | Delay sebelum melakukan input (sekon)                              | Integer   |
| delay_after_program       | Delay setelah melaksanakan sub-program (sekon)                     | Integer   |
| delay_msg                 | Delay untuk menampilkan pesan (sekon)                              | Integer   |

</div>

<div class="cell markdown" id="R0j5v_ORKIoc">

### **Main Menu**

</div>

<div class="cell markdown" id="FnjrqC9IKMWO">

> Penjalanan aplikasi bisa dimulai darisini

</div>

<div class="cell code" id="WLUlltPd3-Sy">

``` python
def menu():
    while True:
        print(f"{config['title']}\n{config['msg_choise']}")
        print(f"[0] {config['exit_choise']}")
        for i, v in enumerate(config['menu']):
            print(f"[{i + 1}] {v['title']}")
        choise = input(config['msg_choise'])
        if choise == '0':
            break
        if choise in tuple(map(lambda x: str(x), range(1, len(config['menu']) + 1))):
            choise = int(choise) - 1
            while True:
                print(f"{config['msg_choise']}\n-> {config['menu'][choise]['title']}")
                print(f"[0] {config['back_choise']}")
                for i, v in enumerate(config['menu'][choise]['lists']):
                    print(f"[{i + 1}] {v}")
                sub_choise = input(config['msg_choise'])
                if sub_choise == '0':
                    break
                if sub_choise in tuple(map(lambda x: str(x), range(1, len(config['menu'][choise]['lists']) + 1))):
                    sub_choise = int(sub_choise) - 1
                    config['menu'][choise]['routes'][sub_choise]()
                    break
                else:
                    print(config['msg_not_in_choise'])
        else:
            print(config['msg_not_in_choise'])
    print(config['msg_exit'])

if __name__ == '__main__':
    from IPython.display import Javascript
    display(Javascript('''google.colab.output.setIframeHeight(0, true, {maxHeight: 5000})'''))
    menu()
```

</div>
