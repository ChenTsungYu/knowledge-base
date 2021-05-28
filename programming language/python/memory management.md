# 記憶體管理
- Python 是一門支援 automatic memory management 的語言，除了可以節省心力提升開發速度外，也可以避免忘記釋出記憶體空間或是過早釋出的問題。
- 這部分內容是以較為廣泛被採用的 CPython implementation 為主。

## 垃圾回收
- 回收已經不需使用的記憶體空間是其中一個重要的工作，Python 同樣有自動垃圾回收的機制，主要有兩個不同的演算法：

### Reference Counting
- 是 CPython 中主要的垃圾回收機制。
- Python 中的每個物件都有其生命週期，而在程式碼中使用的 variable（或稱作 identifier）都是對物件的引用，每多被引用一次該物件的 reference count 就會增加一（比如 assignment operator, argument passing），而取消引用後就會減一。
  - 個別 variable 的 reference count 可用 `sys.getrefcount` 來查看。
  - 在 global scope 所宣告的變數通常會一直留存著，可以使用 `global()` 函數來查看，這也表示對 global variable 所 reference 的物件其 reference count 都不會降至 0，因此儘可能別大量使用 global variable。
  - 在 local scope 比如 class 或 funciton 內所宣告的變數，只要 Python interpreter 離開了該 block 便會移除 scope 內 variable 對物件的引用。
  - 換句話說ㄝ當 Python interpreter 正執行到一個 function 中時，他會假設該 function scope 內的所有 variables 都在使用中而不會移除他們對物件的 reference，因此勁量別讓單個函數過於繁重冗長以避免長時間無法釋放閒置中的 reference。
- 
- Python 會挑選合適的時機點回收掉 reference count 降至 0 的物件。
- 這做法的好處是不需要進行大面積的掃描來偵測可回收物件而影響執行效能，但仍有些如 Cyclic reference 會無法進行處理。

### Generational Garbage Collection