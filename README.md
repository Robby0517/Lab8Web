# Lab8Web penjelasan dari setiap langkah praktikum
beserta screenshotnya.

Nama : Robi Jaelani
NIM : 312310517

Membuat database 
CREATE DATABASE latihan1;

![image](https://github.com/user-attachments/assets/46678e08-56c8-4eb1-b623-2127b3183d03)


Membuat tabel
    CREATE TABLE data_barang (
    id_barang int(10) auto_increment Primary Key,
    kategori varchar(30),
    nama varchar(30),
    gambar varchar(100),
    harga_beli decimal(10,0),
    harga_jual decimal(10,0),
    stok int(4)
    );


![image](https://github.com/user-attachments/assets/d33f7736-5d3c-40e4-805c-70faecfefba8)

Codingan Koneksi.php berfungsi untuk mengkoneksikan ke localhost

    <?php
    $host = "localhost";
    $user = "root";
    $pass = "";
    $db   = "latihan1";
    
    $conn = mysqli_connect($host, $user, $pass, $db);
    
    if ($conn == false) {
        echo "Koneksi ke server gagal.";
        die();
    } else {
        echo "Koneksi berhasil";
    }
    ?>
    Tampilan setelah di koneksikan

![image](https://github.com/user-attachments/assets/23f23274-8e43-402a-aaaf-2e48a38338aa)

Codingan index.php bertujuan untuk membuat data barang

    <?php
    include("koneksi.php");
    
    // Query untuk menampilkan data
    $sql = 'SELECT * FROM data_barang';
    $result = mysqli_query($conn, $sql);
    
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Data Barang</title>
    </head>
    <body>
    <div class="container">
        <h1>Data Barang</h1>
        <a href="tambah_barang.php">Tambah Barang</a>
        <div class="main">
            <table border="1" cellspacing="0" cellpadding="10">
                <tr>
                    <th>Gambar</th>
                    <th>Nama Barang</th>
                    <th>Kategori</th>
                    <th>Harga Jual</th>
                    <th>Harga Beli</th>
                    <th>Stok</th>
                    <th>Aksi</th>
                </tr>
                <?php if($result && mysqli_num_rows($result) > 0): ?>
                    <?php while($row = mysqli_fetch_assoc($result)): ?>
                        <tr>
                            <td><img src="gambar/<?= $row['gambar']; ?>" alt="<?= $row['nama']; ?>" width="100"></td>
                            <td><?= $row['nama']; ?></td>
                            <td><?= $row['kategori']; ?></td>
                            <td><?= number_format($row['harga_jual'], 0, ',', '.'); ?></td>
                            <td><?= number_format($row['harga_beli'], 0, ',', '.'); ?></td>
                            <td><?= $row['stok']; ?></td>
                            <td>
                                <a href="ubah_barang.php?id=<?= $row['id_barang']; ?>">Ubah</a> | 
                                <a href="hapus_barang.php?id=<?= $row['id_barang']; ?>" onclick="return confirm('Yakin ingin menghapus data?')">Hapus</a>
                            </td>
                        </tr>
                    <?php endwhile; ?>
                <?php else: ?>
                    <tr>
                        <td colspan="7">Belum ada data</td>
                    </tr>
                <?php endif; ?>
            </table>
        </div>
    </div>
    </body>
    </html>

Tampilan data barang yang dibuat

 ![image](https://github.com/user-attachments/assets/32d014e8-83fd-414e-93fc-10e4d1ace2d9)

Codingan tambah.php berpungsi untuk menambahkan barang 

    <?php
    error_reporting(E_ALL);
    include_once 'koneksi.php';
    
    if (isset($_POST['submit'])) {
        $nama = mysqli_real_escape_string($conn, $_POST['nama']);
        $kategori = mysqli_real_escape_string($conn, $_POST['kategori']);
        $harga_jual = mysqli_real_escape_string($conn, $_POST['harga_jual']);
        $harga_beli = mysqli_real_escape_string($conn, $_POST['harga_beli']);
        $stok = mysqli_real_escape_string($conn, $_POST['stok']);
        $file_gambar = $_FILES['file_gambar'];
        $gambar = null;
    
        // Validasi file gambar
        if ($file_gambar['error'] == 0) {
            $allowed_extensions = ['jpg', 'jpeg', 'png', 'gif'];
            $file_info = pathinfo($file_gambar['name']);
            $extension = strtolower($file_info['extension']);
    
            if (in_array($extension, $allowed_extensions)) {
                $filename = str_replace(' ', '_', $file_gambar['name']);
                $destination = dirname(__FILE__) . '/gambar/' . $filename;
                if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
                    $gambar = 'gambar/' . $filename;
                } else {
                    echo "Gagal mengunggah gambar.";
                    exit;
                }
            } else {
                echo "Format file tidak valid. Hanya diperbolehkan: " . implode(', ', $allowed_extensions);
                exit;
            }
        }
    
        // Validasi input kosong
        if (empty($nama) || empty($kategori) || empty($harga_jual) || empty($harga_beli) || empty($stok)) {
            echo "Semua field wajib diisi!";
            exit;
        }
    
        // Insert data
        $sql = "INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) 
                VALUES ('{$nama}', '{$kategori}', '{$harga_jual}', '{$harga_beli}', '{$stok}', '{$gambar}')";
        if (mysqli_query($conn, $sql)) {
            header('Location: index.php');
            exit;
        } else {
            echo "Gagal menyimpan data: " . mysqli_error($conn);
            exit;
        }
    }
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <link href="style.css" rel="stylesheet" type="text/css" />
        <title>Tambah Barang</title>
    </head>
    <body>
    <div class="container">
        <h1>Tambah Barang</h1>
        <div class="main">
            <form method="post" action="tambah.php" enctype="multipart/form-data">
                <div class="input">
                    <label>Nama Barang</label>
                    <input type="text" name="nama" required />
                </div>
                <div class="input">
                    <label>Kategori</label>
                    <select name="kategori" required>
                        <option value="">Pilih Kategori</option>
                        <option value="Komputer">Komputer</option>
                        <option value="Elektronik">Elektronik</option>
                        <option value="Hand Phone">Hand Phone</option>
                    </select>
                </div>
                <div class="input">
                    <label>Harga Jual</label>
                    <input type="number" name="harga_jual" required />
                </div>
                <div class="input">
                    <label>Harga Beli</label>
                    <input type="number" name="harga_beli" required />
                </div>
                <div class="input">
                    <label>Stok</label>
                    <input type="number" name="stok" required />
                </div>
                <div class="input">
                    <label>File Gambar</label>
                    <input type="file" name="file_gambar" accept="image/*" />
                </div>
                <div class="submit">
                    <input type="submit" name="submit" value="Simpan" />
                </div>
            </form>
        </div>
    </div>
    </body>
    </html>

Input untuk menambah barang

![image](https://github.com/user-attachments/assets/230fb2d4-0c01-47f5-acc5-64e135c67e1f)

Setelah ditambahkan barang
    
![image](https://github.com/user-attachments/assets/f7bd0d1c-2e1c-450f-bd81-080a1e45f393)
    
    
Codingan ubah.php berfungsi untuk mengubah barang yang sudah ada.
    
    <?php
    error_reporting(E_ALL);
    include_once 'koneksi.php';
    
    if (isset($_POST['submit'])) {
        $id = $_POST['id'];
        $nama = $_POST['nama'];
        $kategori = $_POST['kategori'];
        $harga_jual = $_POST['harga_jual'];
        $harga_beli = $_POST['harga_beli'];
        $stok = $_POST['stok'];
        $file_gambar = $_FILES['file_gambar'];
        $gambar = null;
    
        // Cek jika file gambar diupload
        if ($file_gambar['error'] == 0) {
            $filename = str_replace(' ', '_', $file_gambar['name']);
            $destination = dirname(__FILE__) . '/gambar/' . $filename;
            if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
                $gambar = 'gambar/' . $filename;
            }
        }
    
        // Membuat query untuk update data barang
        $sql = 'UPDATE data_barang SET ';
        $sql .= "nama = '{$nama}', kategori = '{$kategori}', ";
        $sql .= "harga_jual = '{$harga_jual}', harga_beli = '{$harga_beli}', stok = '{$stok}' ";
        
        // Jika ada gambar baru, update juga gambar
        if (!empty($gambar)) {
            $sql .= ", gambar = '{$gambar}' ";
        }
    
        $sql .= "WHERE id_barang = '{$id}'";
        $result = mysqli_query($conn, $sql);
        if ($result) {
            header('location: index.php'); // Arahkan kembali ke halaman utama setelah update
        } else {
            die('Error: Gagal melakukan update');
        }
    }
    
    $id = $_GET['id'];
    $sql = "SELECT * FROM data_barang WHERE id_barang = '{$id}'";
    $result = mysqli_query($conn, $sql);
    if (!$result) die('Error: Data tidak tersedia');
    $data = mysqli_fetch_array($result);
    
    // Fungsi untuk memilih kategori yang dipilih pada dropdown
    function is_select($var, $val) {
        if ($var == $val) return 'selected="selected"';
        return false;
    }
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <link href="style.css" rel="stylesheet" type="text/css" />
        <title>Ubah Barang</title>
    </head>
    <body>
        <div class="container">
            <h1>Ubah Barang</h1>
            <div class="main">
                <form method="post" action="ubah.php" enctype="multipart/form-data">
                    <div class="input">
                        <label>Nama Barang</label>
                        <input type="text" name="nama" value="<?php echo $data['nama']; ?>" />
                    </div>
                    <div class="input">
                        <label>Kategori</label>
                        <select name="kategori">
                            <option <?php echo is_select('Komputer', $data['kategori']); ?> value="Komputer">Komputer</option>
                            <option <?php echo is_select('Elektronik', $data['kategori']); ?> value="Elektronik">Elektronik</option>
                            <option <?php echo is_select('Hand Phone', $data['kategori']); ?> value="Hand Phone">Hand Phone</option>
                        </select>
                    </div>
                    <div class="input">
                        <label>Harga Jual</label>
                        <input type="text" name="harga_jual" value="<?php echo $data['harga_jual']; ?>" />
                    </div>
                    <div class="input">
                        <label>Harga Beli</label>
                        <input type="text" name="harga_beli" value="<?php echo $data['harga_beli']; ?>" />
                    </div>
                    <div class="input">
                        <label>Stok</label>
                        <input type="text" name="stok" value="<?php echo $data['stok']; ?>" />
                    </div>
                    <div class="input">
                        <label>File Gambar</label>
                        <input type="file" name="file_gambar" />
                    </div>
                    <div class="submit">
                        <input type="hidden" name="id" value="<?php echo $data['id_barang']; ?>" />
                        <input type="submit" name="submit" value="Simpan" />
                    </div>
                </form>
            </div>
        </div>
    </body>
    </html>

Input untuk mengubah barang

![image](https://github.com/user-attachments/assets/107997d5-f98b-4ec7-bf75-d3718e2039fb)

Setelah dirubah

![image](https://github.com/user-attachments/assets/2616c2af-867f-48ab-8825-ea3066dc93b9)

Codingan hapus.php berfungsi untuk menghapus barang yang ingin dihapus

    <?php
    // Sertakan file koneksi
    include_once 'koneksi.php';
    
    // Validasi parameter ID
    if (isset($_GET['id']) && is_numeric($_GET['id'])) {
        $id = mysqli_real_escape_string($conn, $_GET['id']);
    
        // Query untuk menghapus data
        $sql = "DELETE FROM data_barang WHERE id_barang = '{$id}'";
        $result = mysqli_query($conn, $sql);
    
        // Cek apakah query berhasil
        if ($result) {
            header('Location: index.php'); // Redirect ke halaman utama
            exit;
        } else {
            echo "Gagal menghapus data: " . mysqli_error($conn);
        }
    } else {
        echo "ID tidak valid.";
        exit;
    }
    ?>


Ketika sudah dihapus

![image](https://github.com/user-attachments/assets/3e580c64-f6f0-4cdf-9e59-890bc255ee62)

