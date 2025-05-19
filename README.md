package main

import (
	"fmt"
	"strings"
	"time"
)

type Polusi struct {
	Lokasi        string
	Tanggal       time.Time
	AQI           int
	Sumber        string
	TingkatBahaya string
}

var dataPolusi []Polusi

func main() {
	for {
		fmt.Println("\n=== Menu Aplikasi Pemantauan Polusi Udara ===")
		fmt.Println("1. Tambah Data")
		fmt.Println("2. Ubah Data")
		fmt.Println("3. Hapus Data")
		fmt.Println("4. Tampilkan Semua Data")
		fmt.Println("5. Urutkan AQI (Selection Sort)")
		fmt.Println("6. Urutkan AQI (Insertion Sort)")
		fmt.Println("7. Cari Kota (Sequential Search)")
		fmt.Println("8. Cari Kota (Binary Search)")
		fmt.Println("9. Tampilkan Data Berdasarkan Status")
		fmt.Println("0. Keluar")
		fmt.Print("Pilih menu: ")

		var pilihan int
		fmt.Scanln(&pilihan)

		switch pilihan {
		case 1:
			tambahData()
		case 2:
			ubahData()
		case 3:
			hapusData()
		case 4:
			tampilkanSemua()
		case 5:
			selectionSort()
			fmt.Println("Data telah diurutkan (Selection Sort).")
		case 6:
			insertionSort()
			fmt.Println("Data telah diurutkan (Insertion Sort).")
		case 7:
			var kota string
			fmt.Print("Masukkan nama kota: ")
			fmt.Scanln(&kota)
			cariSequential(kota)
		case 8:
			var kota string
			fmt.Print("Masukkan nama kota: ")
			fmt.Scanln(&kota)
			cariBinary(kota)
		case 9:
			var status string
			fmt.Print("Masukkan status (Baik/Sedang/Tidak Sehat/Berbahaya): ")
			fmt.Scanln(&status)
			tampilkanBerdasarkanStatus(status)
		case 0:
			fmt.Println("Keluar dari aplikasi.")
			return
		default:
			fmt.Println("Pilihan tidak valid.")
		}
	}
}

func tambahData() {
	var lokasi, sumber, tanggal string
	var aqi int

	fmt.Print("Lokasi: ")
	fmt.Scanln(&lokasi)
	fmt.Print("Tanggal (YYYY-MM-DD): ")
	fmt.Scanln(&tanggal)
	fmt.Print("AQI: ")
	fmt.Scanln(&aqi)
	fmt.Print("Sumber Polusi: ")
	fmt.Scanln(&sumber)

	tgl, err := time.Parse("2006-01-02", tanggal)
	if err != nil {
		fmt.Println("Format tanggal salah.")
		return
	}
	status := kategoriPolusi(aqi)

	dataPolusi = append(dataPolusi, Polusi{lokasi, tgl, aqi, sumber, status})
	if aqi > 100 {
		fmt.Println("⚠️  Peringatan! AQI melebihi batas aman:", status)
	}
}

func ubahData() {
	if len(dataPolusi) == 0 {
		fmt.Println("Data masih kosong.")
		return
	}

	tampilkanSemua()
	var idx int
	fmt.Print("Masukkan indeks data yang akan diubah: ")
	fmt.Scanln(&idx)

	if idx < 0 || idx >= len(dataPolusi) {
		fmt.Println("Indeks tidak valid.")
		return
	}

	var lokasi, sumber, tanggal string
	var aqi int

	fmt.Print("Tanggal baru (YYYY-MM-DD): ")
	fmt.Scanln(&tanggal)
	fmt.Print("Lokasi baru: ")
	fmt.Scanln(&lokasi)
	fmt.Print("AQI baru: ")
	fmt.Scanln(&aqi)
	fmt.Print("Sumber baru: ")
	fmt.Scanln(&sumber)

	tgl, err := time.Parse("2006-01-02", tanggal)
	if err != nil {
		fmt.Println("Format tanggal salah.")
		return
	}

	status := kategoriPolusi(aqi)
	dataPolusi[idx] = Polusi{lokasi, tgl, aqi, sumber, status}
	fmt.Println("Data berhasil diubah.")
}

func hapusData() {
	if len(dataPolusi) == 0 {
		fmt.Println("Data masih kosong.")
		return
	}

	tampilkanSemua()
	var idx int
	fmt.Print("Masukkan indeks data yang akan dihapus: ")
	fmt.Scanln(&idx)

	if idx < 0 || idx >= len(dataPolusi) {
		fmt.Println("Indeks tidak valid.")
		return
	}

	dataPolusi = append(dataPolusi[:idx], dataPolusi[idx+1:]...)
	fmt.Println("Data berhasil dihapus.")
}

func tampilkanSemua() {
	if len(dataPolusi) == 0 {
		fmt.Println("Belum ada data polusi.")
		return
	}

	fmt.Println("=== Data Polusi ===")
	for i, d := range dataPolusi {
		fmt.Printf("[%d] %s | %s | AQI: %d | Sumber: %s | Status: %s\n",
			i, d.Lokasi, d.Tanggal.Format("2006-01-02"), d.AQI, d.Sumber, d.TingkatBahaya)
	}
}

func kategoriPolusi(aqi int) string {
	switch {
	case aqi <= 50:
		return "Baik"
	case aqi <= 100:
		return "Sedang"
	case aqi <= 150:
		return "Tidak Sehat"
	default:
		return "Berbahaya"
	}
}

func selectionSort() {
	n := len(dataPolusi)
	for i := 0; i < n-1; i++ {
		maxIdx := i
		for j := i + 1; j < n; j++ {
			if dataPolusi[j].AQI > dataPolusi[maxIdx].AQI {
				maxIdx = j
			}
		}
		dataPolusi[i], dataPolusi[maxIdx] = dataPolusi[maxIdx], dataPolusi[i]
	}
}

func insertionSort() {
	for i := 1; i < len(dataPolusi); i++ {
		key := dataPolusi[i]
		j := i - 1
		for j >= 0 && dataPolusi[j].AQI > key.AQI {
			dataPolusi[j+1] = dataPolusi[j]
			j--
		}
		dataPolusi[j+1] = key
	}
}

func cariSequential(kota string) {
	found := false
	for _, d := range dataPolusi {
		if strings.EqualFold(d.Lokasi, kota) {
			fmt.Printf("%s | AQI: %d | Status: %s\n", d.Lokasi, d.AQI, d.TingkatBahaya)
			found = true
		}
	}
	if !found {
		fmt.Println("Kota tidak ditemukan.")
	}
}

func cariBinary(kota string) {
	// Sorting berdasarkan lokasi
	for i := 0; i < len(dataPolusi)-1; i++ {
		for j := i + 1; j < len(dataPolusi); j++ {
			if strings.ToLower(dataPolusi[i].Lokasi) > strings.ToLower(dataPolusi[j].Lokasi) {
				dataPolusi[i], dataPolusi[j] = dataPolusi[j], dataPolusi[i]
			}
		}
	}

	low, high := 0, len(dataPolusi)-1
	for low <= high {
		mid := (low + high) / 2
		compare := strings.Compare(strings.ToLower(kota), strings.ToLower(dataPolusi[mid].Lokasi))
		if compare == 0 {
			fmt.Printf("%s | AQI: %d | Status: %s\n", dataPolusi[mid].Lokasi, dataPolusi[mid].AQI, dataPolusi[mid].TingkatBahaya)
			return
		} else if compare > 0 {
			low = mid + 1
		} else {
			high = mid - 1
		}
	}
	fmt.Println("Kota tidak ditemukan.")
}

func tampilkanBerdasarkanStatus(status string) {
	status = strings.ToLower(status)
	found := false
	for _, d := range dataPolusi {
		if strings.ToLower(d.TingkatBahaya) == status {
			fmt.Printf("%s | %s | AQI: %d | Sumber: %s\n", d.Lokasi, d.Tanggal.Format("2006-01-02"), d.AQI, d.Sumber)
			found = true
		}
	}
	if !found {
		fmt.Println("Tidak ada data dengan status tersebut.")
	}
}
