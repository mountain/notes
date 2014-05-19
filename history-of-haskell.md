本文翻譯自Paul Hudak、John Hughes、Simon Peyton Jones、Philip Wadler今年在第三屆
ACM SIGPLAN History of Programming Languages Conference (HOPL-III)上的論文
A History of Haskell: being lazy with class，因為原文較長，我將陸續翻譯過來。

Haskell史傳：帶類的惰性語言
-------------------------

二○○七年一月二十五日

Paul Hudak（耶魯大學）、John Hughes（查爾默斯大學）、Simon Peyton Jones（微軟研究院）、
Philip Wadler（愛丁堡大學）

#### 摘要

本論文描述了Haskell語言的歷史，包括了它的創始和原則、技術貢獻、實現與工具和應用與影響。

#### 1. 介紹

“1987年9月，在俄勒岡的波特蘭召開的函數式語言與計算機體系結構大會
（the conference on Functional Programming Languages and Computer Architecture）
上舉行了一次會議，來討論函數式程序設計社群的一個不幸的現實：已經有成打的非嚴格的
（non-strict）、純函數式程序設計語言正在出現了，所有這些語言在表達力和支撐的語義上都很接近。
缺乏一個共同的語言阻礙了這一類函數式語言獲得更廣泛的使用，與會者對此形成了很強烈的共識。
他們決定建立一個委員會來設計這種語言，以此促成新觀點更快的交流，提供真正應用開發的穩定基礎，
以及可以鼓勵外界使用函數式語言的一種工具。”

這一開篇之語，載于1990年4月間的第一份Haskell報告（Haskell Report）的第一個版本，多少道出
了些Haskell的來歷。它確立了設計Haskell的動機（需要一個共同的語言），被設計的語言的本質
（非嚴格的、純函數式的），以及語言如何被設計的過程（通過委員會）。

本文的第一部分描述Haskell的創始與原則：Haskell如何演化而來。我們描述了導致Haskell出現的
各種技術發展以及Haskell的早期歷史，（第2節）給出了過程和原則，它們指導了Haskell的演化
（第3節）。

第二部分描述Haskell的技術貢獻：Haskell是甚麼？我們特別關注那些語言與演化上與眾不同的方面，
或者沒有按照期望的、以令人驚奇方式發展的那些方面。我們反思五個領域：語法（第4節）；
代數數據類型（第5節）；類型系統，特別是類型類（第6節）；Monads與輸入/輸出（第7節）；
大型編程的支持設施，如模塊（modules）與包（packages），以及外部函數接口（第8節）。

第三部分描述實現與工具：我們為Haskell用戶創建了什么？我們描述了Haskell的不同實現，
包括GHC、hbc、hugs、nhc和Yale Haskell（第9節），以及性能分析（profiling）和調試
（debugging）的工具（第10節）。

第四部分描述應用與影響：Haskell語言的用戶都開發了什么？這門語言已經被用來開發出令人
眼花繚亂的、各式各樣的應用，在第11節我們會思考某些應用里與眾不同的方面，只要我們能辨明它們。
我們在這一節中得出結論，評估Haskell對不同用戶群體的影響，如教育界、開放源代碼、公司和
其他語言的設計者們（第12節）。

貫穿本文的我們的目標就是講述故事，包括誰參與了，甚么給與他們靈感：本文應該被視為歷史，而不是
技術描述或者教程。

我們試圖用一種不偏不倚的方式描述Haskell的演化，但通過包含一些軼事與個人思考，我們依然尋求
傳達出這個過程中的興奮與激情。無可避免的，尋求這種活潑的格調意味著我們的記敘將偏重於我們個人
參與的會議或者談話。然而，我們亦知許多、許多人對Haskell貢獻頗多。Haskell社群的大小與品質，
它的寬度與深度，都是Haskell成功的指標，也是它成功的原因。

一個必然的短處就是缺少全面的描述。Haskell至今已發明15年了，它已經成為無數創造力的苗床。
我們無法期望公平的對待所有它們，但我們借此機會向所有作出貢獻的人致敬，
Haskell如今已化為一匹野馬。

### 第一部分 創始與原則

#### 2. Haskell的創始

1978年John Backus發表了他的Turing獎演講—
“程序設計能從von Neumann風格中獲得解放嗎？”（Backus, 1978a）。該文對整個程序設計業界
（programming enterprise），從硬件體系結構以上，，用函數式程序設計作了激進的抨擊。
Backus曾領導一個小組開發了Fortran，并且發明了 Backus-Naur范式，作為這個領域的巨人，
他顯著的認可使函數式程序設計以一種新的方式而聞名，不但是一種數學上的好奇，更是一種實用的
編程工具。

即使在這個階段，函數式程序設計也已經擁有一段長長的歷史。這歷史開端於1950年代末期
John McCarthy對Lisp的發明（McCarthy, 1960）。在1960年代，Peter Landin和
Christopher Strachey確認了lambda演算對程序設計語言建模的基礎性重要地位，并且建立了
操作語義與指稱語義（Strachey,1964）的基礎，前者是通過抽象機器達成的(Landin, 1964)。
幾年之后，Strachey與Dana Scott的合作研究把指稱語義置于了堅實的數學基礎之上，
該基礎又由Scott的域理論（domain theory, Scott and Strachey, 1971; Scott, 1976）得以
加強。在70年代早期，Rod Burstall與John Darlington正通過一種一階函數式語言來進行程序轉換
（program transformation），這種語言通過模式匹配（pattern matching）來定義函數
（Burstall and Darlington, 1977）。

在同一時段，曾經是Strachey一名學生的David Turner開發了SASL（Turner, 1976），一種純的、
高階函數式、有詞法作用域變量（lexically scoped variables）的語言—一種從Landin的ISWIM
（Landin, 1966）的一個可應用的子集衍生出來的糖化（sugared）了的lambda演算—
這種語言把Burstall與Darlington關于模式匹配的想法貫徹入一種可執行的程序設計語言。

在70年代晚期，Gerry Sussman和Guy Steele開發了Scheme，Lisp的一種方言，通過實現了
詞法作用域（lexical scoping），這種方言更貼近於lambda演算
（Sussman and Steele, 1975; Steele, 1978）。在或多或少同一時間，Robin Milner發明了
ML，為愛丁堡的定理證明機LCF來作為一種元語言（Gordon et al., 1979）。
Milner為ML發明的多態類型系統將被證明是特別有影響的（Milner, 1978;Damas, Milner, 1982）
。Scheme和ML都是嚴格的（傳值的，callby-value）語言，盡管它們都包含命令式的（imperative）
特征，它們對提升函數式程序設計風格的貢獻頗多，特別是在高階函數的使用上。

##### 2.1 惰性的呼喚

接著在70年代晚期和80年代早期，新事物出現了。一系列獨創性的出版物引起人們爆炸般的興趣—把惰性
（非嚴格的，或call-by-need）函數式語言作為一種書寫嚴肅程序工具的興趣。
惰性求值（lazy evaluation）看來被獨立發明了三次。

* Dan Friedman和David Wise（都在Indiana），發表了《Cons不應求其參數的值》
（Friedman和Wise,1976），這篇論文從Lisp的角度來看待惰性求值。
* Peter Henderson（在Newcastle)和James H. Morris Jr.（Xerox PARC）發表了
《一個惰性求值器》（Henderson和Morris, 1976）。作為思想上的來源，
他們參引了Vuillemin（Vuillemin, 1974）和Wadsworth（Wadsworth, 1971），
但是是他們在POPL會議上使這個思想廣為人知，并且他們另外還有一個重要貢獻—這個思想的名字。
他們也使用了Lisp的一種變體（variant），并展示了他們的求值器在指稱語義下的
完備（soundness）。
* David Turner（在 St. Andrews和Kent）引入了一系列有影響的語言：
SASL（St Andrews Static Language）（Turner, 1976），該語言最初在1972年被設計成一種
嚴格的語言，但在1976年則變成一種惰性語言；還有KRC（Kent Recursive Calculator）
（Turner, 1982）。Turner展示了惰性求值編程的優美，特別是通過惰性列表（lazy lists）來
模擬許多不同類型的行為（Turner, 1981; Turner, 1982）。在Burroughs，SASL甚至被用來開發
一整個操作系統—這幾乎肯定是最早的、大規模的純惰性函數式編程的練習。

與此同時，人們也息息相關的致力於以令人激動的新方式實現惰性語言。

* 在軟件方面，一些基于圖規約（graph reduction）的不同技術得到了探索，特別是Turner，
他頗有靈感的、優美的使用了SK組合子（SK combinators）（Turner, 1979b; Turner, 1979a）。
（Turne的工作基于Haskell Curry的組合子演算（combinatory calculus）
（Curry和Feys, 1958），該演算是Alonzo Church的lambda演算（Church, 1941）的一種
無變量版本。）
* 另外一個強有力的因素是這樣一種可能性，所有這些將導致一種激進的非von Neumann的硬件體系
結構。幾個建造不同類型數據流機或者圖規約機（graph reduction machines）的認真計劃正在
進行（或將進行），它們包括MIT的Id計劃（Arvind和Nikhil, 1987），Utah的Rediflow計劃
（Keller et al., 1979），劍橋的SK組合子機SKIM（Stoye et al.,1984），
Manchester數據流機（Watson and Gurd,1982），帝國理工（Imperial）的ALICE并行規約機
（Darlington和Reeve, 1981），Burroughs的NORMA組合子機（Scheevel, 1986），
以及Utah的DDM數據流機（Davis, 1977）。當后來發現好的面向棧結構
（stock architecture？疑為stack architecture）的編譯器可勝出於特殊的體系結構之時，
大部分（但不是所有）面向體系結構的工作都轉向一個死亡的結局。但在那時，一切都是那么激進和振奮。

幾個重大的會議也出現在80年代，這些會議給這個領域更多的推動力。

在1980年8月，首届Lisp会议（Lisp conference）在加利福尼亚的Stanford召开。
讲演包括了Rod Burstall、Dave MacQueen和Don Sannella的Hope，一种包括代数数据类型
（algebraic data types）的语言（Burstall et al., 1980a）。

在1981年7月，Peter Henderson、John Darlington和David Turner在Newcastle開辦了一個關于
函數式程序設計及應用的高級課程（Darlington et al., 1982）。所有知名人物都在其中：
參與者包括Gerry Sussman、Gary Lindstrom、David Park、Manfred Broy,、Joe Stoy和
Edsger Dijkstra。（Hughes和Peyton Jones作為學生參加）。
Dijkstra特別表示未留下很深印象—他寫道：“整體而言，我不能掩飾深深的失望之情。我仍然相信這個
論題值得更多更適當的對待；但確信無疑的，相當多我們展現出來的沒有達到一定水準。”
（Dijkstra, 1981）—但對許多與會者來說，這次會議是一個分水嶺。

在1981年9月，首屆函數式程序設計語言與計算機體系結構會議
（conference on Functional Programming Languages and Computer Architecture，FPCA）
—請留意標題—在新漢普郡的Portsmouth召開。這次Turner發表了他頗有影響的論文
《可應用語言語義的優美性》（The semanticelegance of applicative languages）
（Turner, 1981）。（Wadler也在這次會議上發表了他第一篇論文。）
FPCA成為了這個領域關鍵性的雙年會議。

在1982年9月，第二屆Lisp會議在賓夕法尼亞的Pittsburgh召開，會議更名為Lisp和函數式程序設計
（Lisp and Functional Programming，LFP）。講演包括了Peter Henderson的關于
函數幾何（functional geometry）（Henderson, 1982），Turner的特邀演講—關于無窮數據
結構（infinite data structures）上的編程。（這次也看到Hudak、Hughes和Peyton Jones
第一次發表論文）。這次會議的特約嘉賓包括Church和Curry。Barkley Rosser作了晚飯后的演講，
演講當中他受到兩次歡呼：一次是當他給出Curry悖論（Curry’s paradox）的證明并將之與Y組合子
（Y combinator）聯系起來，另一次是他展示了Church-Rosser定理（Church-Rosser theorem）
的一個新證明。LFP成為另一個關鍵性的雙年會議。

（在1996年，FPCA與LFP合并至一個每年一度的函數式程序設計國際會議，ICFP，直至今日這個會議仍然
是這個領域的關鍵性會議。）

在1987年8月，作為Texas大學“編程之年”的一部分，Texas大學的Ham Richards和David Turner在
得克萨斯州的Austin組織了一次關于聲明式程序設計（Declarative Programming）的國際學校。
講者包括了Samson Abramsky、John Backus、Richard Bird、Peter Buneman、
Robert Cartwright、Simon Thompson、David Turner和Hughes。這個學校的主要設置是一門惰性
函數式程序設計的課程，而且还有采用了Miranda的實習课。

所有這些帶來了巨大的興奮感。函數式程序設計的簡潔與優美俘虜了現在的作者們以及和他们一起的許多
其他研究者。與惰性求值直接聯系的是，純的、按名調用（call-by-name）的lambda演算，可表示并
操作無窮數據結構的不同凡響的可能性，以及引人的簡單而優美的實現技術，所有这些就像一劑毒品一样
使人上癮。

（一個匿名的評審者作出了如下表述：“一個有趣的杂闻是Friedman和Wise的論文曾经鼓舞Sussman和
Steele檢查Scheme中的惰性求值，并且有段時間他們在權衡，在Schema的一個版本中，究竟采用按名
調用還是按值調用。比之在按名調用的語言中模擬按值調用（需要一種分離的強制求值機制），在一個
按值調用的語言中更易於實現按名調用（把lambda表達式作為thunks），考慮到此，他們最終選擇保留
原有的按值調用的設計方案。不論我們如何看待這個论证，假如他們做了相反的決定，我們唯一能作的
推想就是，現今的學術性程序設計語言將會多么的不同呀！”）

##### 2.2 巴別之塔

作為所有上述活動的結果，在1980年代中期，一些研究者，包括作者們，對純的惰性語言的設計與實現
有著熱切的興趣。實際上，我們中的相當多的人都獨立設計了自己的惰性語言，并忙于建造自己的實現。
我們每個都撰寫論文匯報成果，在其中我們總是先描述我們的語言，再給出我們的實現技術。
對這座惰性巴別之塔作出貢獻的有：

* Miranda：SASL和KRC的後繼者，由David Turner設計與實現，使用SK組合子規約。Miranda增加了
強多態類型（strong polymorphic typing）和類型推理（type inference），這兩者的想法在ML
中被證明是非常成功的。
* Lazy ML（LML）：初創於Chalmers的Augustsson和Johnsson，其後由倫敦大學學院的
Peyton Jones負責。這個成果包含了有影響的Gmachine的開發，它說明人們可以把惰性函數式程序編譯
成相當快速的代碼（Johnsson, 1984; Augustsson, 1984）（惰性意味著圖規約，圖規約意味著
解釋，盡管回顧起來相當明顯，但在當時，我們是變得習慣起這個思想的。）
* Orwell：一個由Wadler開發的惰性語言，該語言受到了KRC、Miranda以及Orwell後來的一個變體
OL的影響。Bird和 Wadler共同撰寫了一部關于函數式編程的有影響的書籍
（Bird and Wadler, 1988），為了避免“巴別之塔”，該書使用了一種接近于Miranda和Orwell的更
數學化的記法。
* Alfl：由Hudak設計，基于為Scheme和T（Scheme的一種方言）而發展出技術，他在Yale的小組
為Alfl開發了基于組合子的解釋器和編譯器（Hudak, 1984b; Hudak, 1984a）。
* Id：一種由MIT的Arvind和Nikhil開發的非嚴格的數據流語言，這個語言的目標機器是一種他們正在
建造的數據流機。
* Clean：一種顯式的基于圖規約的惰性語言，由Nijmegen的Rinus Plasmeijer及他的同事開發
（Brus et al., 1987）。
* Ponder：一種由Jon Fairbairn設計的語言，帶有非斷言高秩類型系統
（impredicative higher-rank type system）和詞法作用域類型變量，該語言被用來書寫SKIM上
的操作系統（Fairbairn, 1985; Fairbairn, 1982）。
* Daisy：Lisp的一種惰性方言，由Indiana的Cordelia Hall、John O’Donnell和他們的同事開發
（Hall and O’Donnell, 1985）。

除Miranda顯著的與眾不同，所有這些語言都本質上是獨門的（single-site）語言，從語言設計的成果
、實現和用戶方面來講，它們每一個都缺乏這些必不可少的元素（critical mass）。此外，盡管每一個
都擁有許多有趣的想法，但幾乎沒有理由聲明某一個語言可證實的優于其他任何一個。相反的，我們覺得
所有他們除了語法都大體相似，并且我們開始想問為什么我們不能擁有單獨的一個共同的語言呢？這樣
我們所有都可從中獲益。

此時，Scheme和ML社群都發展出他們自己的標準。Scheme社群的主要地點在MIT、Indiana和Yale，
他們剛發布了它的“修訂的修訂”報告（Rees and Clinger, 1986）（後繼的修訂版本直至修訂的5次方
報告（Kelsey et al., 1998））Robin Milner已經發布了“Standard ML提議”（Milner, 1984）
（這個提議將演變成最終的Standard ML定義
（Milner and Tofte, 1990; Milner et al., 1997）），并且Appel和MacQueen已經為它發布了
一個高質量的編譯器（Appel and MacQueen, 1987）。

譯注：impredicative higher-rank type system沒有查到任何中文的標準翻法，只得翻譯為
“非斷言高秩類型系統”，另查到Simon Peyton Jones兩篇論文
Boxy types: type inference for higher-rank types and impredicativity
和Practical type inference for arbitrary-rank types。

##### 2.3 Haskell的誕生

到1987年，境況頗類似于一瓶過冷（super-cooled）的溶液—所有所需要的僅僅是一個隨機事件來促成
結晶過程的發生。這個事件就發生在 87年的秋天，在Peyton Jones前去俄勒岡Portland參加1987年
函數式程序設計語言與計算機體系結構會議（FPCA）的中途，他停在Yale去看Hudak。討論過情形以后，
Peyton Jones和Hudak決定在FPCA發起一個會議，來積累設計一個新的、共同的函數式語言的興趣。
Wadler也在去FPCA的路上停在Yale，并且也支持開會的這個想法。

盡管我們還沒有這個語言的名字并且很少進行技術上的探討或者作出設計上的決定，FPCA會議還是就這樣
標記為Haskell設計過程的開端。實際上，那次會議得出的一個關鍵觀點是，最簡易的推進辦法是以一個
已經存在的語言為開始，并朝適合我們的無論什么方向來演化。在所有這些開發中的惰性語言之中，
顯然David Turner的Miranda是最成熟的。它是純的，設計也良好，可以滿足我們的許多目標，擁有
一個健壯的實現，這個實現是Turner的公司— Research Software Ltd—的產品，并且正在120個地點
運行。Turner沒有出席那次的會議，所以我們決定委員會的第一個活動事項就是去問Turner，看他是否
允許我們采用Miranda作為新語言的起點。

一個簡短而熱忱的意見交換之后，Turner回絕了。他的目標與我們不同。在許多目標之中，我們需要
一個語言可以用來研究語言的特性；特別的，我們尋求任何人來擴展或者改動這個語言的自由，并且也有
建造和發布一個實現的自由。Turner，正相反，致力于維護一個單一的語言標準，確保Miranda 社群內
程序完全的可移植性（portability）。他不想Miranda有多種方言流通，并且請求我們設計一個新的
語言，它與Miranda足夠不同以致兩者不相混淆。Turner也回絕了加入新的設計委員會的邀請。

勿論好壞，這是路途上一個重要的分岔口。盡管這意味著我們不得不作出新語言設計的每一個細枝末節，
而不是從一個發展良好的基礎上開始，但這樣給予了我們自由來忖思語言設計許多方面更激進的通路。
比如，假若我們從Miranda開始，看起來我們不會發展出類型類（參見6.1節）。盡管如此，
Haskell仍然欠重恩于Miranda，既為了大體上的啟示，也為了特定的語言元素，在適合我們醞釀中的
設計之處我們自由的采納了它們。我們會在 3.8節進一步討論Haskell和Miranda的關系。
一旦我們確知Turner不會允許我們使用Miranda，通過架設在倫敦大學學院
（Peyton Jones當時在該校任職）的郵件列表fplangc@cs.ucl.ac.uk，一連串發瘋般熱烈的email
討論迅速相繼發生。郵件列表的名字得名於這個事實，因為我們沒有語言的名字，我們最初稱呼自己為
“FPLang Committee”。直到我們命名了這個語言（2.4節），我們才開始稱呼自己為
“Haskell委員會”。

##### 2.4 首輪會議

Yale會議

第一次實體上的會議（在即兴的FPCA會議之後），於1988年1月9-12日在Yale召開，當時Hudak在那里
任副教授（Associate Professor）。第一要務就是為語言建立如下的目標：

1. 它應當適於教學、研究和應用開發，包括建造大型系統。
2. 通過的發表形式化的語法和語義，它應當得到完全的描述。
3. 它應當可以自由獲取。任何人都應被許可實現這個語言，并可將它分發給任何他們意愿的人。
4. 它應當可以用來作為更進一步語言研究的一個基礎。
5. 它應當以獲得廣泛共識的想法為基礎。
6. 它應當減少函數式程序語言的不必要的多樣性。更明確的說，我們初步同意把它置于一個已經存在的
語言之上，也即OL。

最後兩個目標反映了這個事實，我們計劃設計的語言是相當保守的，而不是要越過新的領地。盡管事情的
進展相當不同，比之加入當前觀點上的共識和團結我們全異的群體於一單一的設計，我們打算的更少。

就像我們能夠看到的，并不是所有這些目標都能被實現。我們很早就拋棄了顯式的把Haskell基于OL的
想法；我們違反了僅包含已經很好嘗試過想法的目標，特別是在采納類型類這個事例上；并且我們也從
沒有開發出形式語義。

我們在第3節討論這些變化發生的方式。這里是我們達成一致的委員會流程（committee process），
直接摘錄自會議紀要。

1. 確定我們想討論的議題，為每一個議題指派一個“領頭人”（lead person）。
2. 領頭人通過為他的議題總結論點來開始討論：
* 特別的，從OL如何做的描述來開始。
* 如果沒有清楚的解決方案存在，OL的將成為缺省的。
3. 我們應當鼓勵中断、側面討論（side discussions）和文獻研究，如果有必要的話。
4. 有些問題將不會被解決！但這種情況下，我們應當為它們的最終解決建立活動事項。
5. 看起來有點不太嚴肅，但我們不能夠休會，直到至少一件事情得到解決：這個語言的名字！
6. 態度很重要：一種合作與妥協的精神。我們會稍晚在3.5節回到委員會設計過程作進一步討論。在
Haskell委員會供職的所有人的名單將會在14節出現。

選擇名字

上面的第五個項目是重要的，因為任何語言演化的小而重要的時刻就是它被命名的那一刻。在Yale會議上
，我們采用了下述流程（由Wadler提議）來選擇名字。

任何人都可為這個語言提議一到多個名字，全部都寫在黑板上。在這個過程結束之後，出現了下述名字：
Semla、Haskell、Vivaldi、Mozart、CFL（Common Functional Language）、Funl 88、
Semlor、Candle (Common Applicative Notation for Denoting Lambda Expressions)、
Fun、David、Nice、Light、ML Nouveau（或Miranda Nouveau，或LML Nouveau，或……）、
Mirabelle、Concord、LL、Slim、Meet、Leval、Curry、Frege、Peano、 Ease、Portland
和Haskell B Curry。對這些名字經過相當的討論之後，每個人都可以自由的劃掉他不喜歡的名字。
當我們做完時，只有一個名字留下來。

那個名字就是“Curry”，這是為了紀念數學家與邏輯學家Haskell B. Curry，正是他的工作，不一而足
和非直接的，引領著我們聚於一堂。當晚，我們中的兩人意識到我們將會碰到大量的雙關語
（除了香料和阿諛奉承，真正讓我們害怕的是Tim Curry—TIM是Jon Fairbairn的抽象機，
而Tim Curry因擔綱主演《洛基恐怖秀》而聞名）。所以第二天，經過更進一步的討論，我們確定
以“Haskell”作為這個新語言的名字。只是到了後來，我們才意識到這個名字太容易和Pascal或Hassle
相混淆！Hudak和Wise被要求給Curry的遺孀—Virginia Curry—寫信，來征詢她是否介意我們以
她丈夫的名字來命名這門語言。Hudak后來到Curry夫人家里拜訪了她，并且聽了待在那里的人們
（比如 Church和Kleene）的故事。Curry夫人回想起他（當然是指Haskell）
在Pennsylvania州立大學的演講，雖然她聽不懂他所說的每一句話，她非常和藹可親。
她在離別時的評注是“你知道，Haskell實際上從不喜歡Haskell這個名字。”

Glasgow會議

Yale會議之後，Email討論繼續如火如荼般的進行，但許多懸而未決的問題仍有待第二次會議來解決。
這次會議於1988年4月6—9日在Glasgow大學舉行，那里的函數式編程小組正開始一段快速成長的時期。
正是在這次會議，許多關鍵的決定被確定下來。

在此次會議上，我們也商定Hudak和Wadler作為首份Haskell報告的編輯。這份報告的名字“關于
程序設計語言Haskell的報告—一個非嚴格的、純的函數式語言”部分的受“關于算法語言Schema的報告”
的啟發，而依次的，后者又是模仿了“關于算法語言Algol的報告”。

IFIP WG2.8會議

八十年代是研究函數式編程的振奮人心的年代。振奮的標志之一便是IFIP關于函數式編程的工作組2.8的
建立，這主要歸功于John Williams（John Backus在IBM Almaden的長期合作者）的努力。
它不但為這個領域帶來正規性（legitimacy），而且提供了一個適宜的會議地點來討論Haskell，
并在 WG2.8會議之前或之後捎帶進行Haskell委員會會議。頭兩屆WG2.8會議分別於1988年7月11-15日
在蘇格蘭Glasgow以及1989 年5月1-5日在美國康奈提格州的Mystic舉行
（Mystic距離Yale大約30分鐘車程）。圖一攝于Oxford的1992年WG2.8會議。

圖略。

##### 2.5 細化設計

最初慌忙的面對面會議之后，是十五年詳細的語言設計和開發，這些活動完全通過電子郵件來協作。
下面是Haskell如何發展的簡要時間線索：

* 1987年9月：在俄勒岡Portlan的FPCA上的最初會議。

* 1987年12月：倫敦大學學院的分組會議。

* 1988年1月：在Yale大學的多日會議。

* 1988年4月：在Glasgow大學的多日會議。

* 1988年7月：在Glasgow的首次IFIP WG2.8會議。

* 1989年5月：在康奈提格州Mystic的第二次IFIP WG2.8會議。

* 1990年4月1日：1.0版的《Haskell報告》出版（125頁），由Hudak和Wadler編輯。
同一時間，Haskell郵件列表開啟，向所有人開放。

關閉的fplangc郵件列表繼續用于委員會討論，但持續增加的爭議都發生在公開的Haskell郵件列表上。
因同時擁有公開和私下的郵件列表帶來了種種“我們與他們”的暗示，這使委員會成員變得愈加不安，
終至1991年4月停止了fplangc郵件列表的使用。所有進一步關于Haskell的討論都公開進行，
但決定依然由委員會給出。

* 1991年8月：1.1版的《Haskell報告》出版（153頁），由Hudak、Peyton Jones和Wadler主編。
這次大體上是個“精簡”版，但它首次包括了let表達式和運算符的章節。

* 1992年3月：1.2版本的Haskell報告出版（164頁），由Hudak、Peyton Jones和Wadler編輯，
與Haskell報告1.1相比僅有小的變化。兩個月后，在1992年5月，這份報告出現在SIGPLAN評論
（SIGPLAN Notices）之中，另有Hudak和Fasel撰寫的《對Haskell的友善介紹》
（Gentle introduction to Haskell）也附加出現。我們非常感激SIGPLAN的主席Stu Feldman，
以及評論的編輯Dick Wexelblatt，因為他們欣然同意在SIGPLAN評論上發表這樣長的一篇文章。
這使得Haskell既引人注目又可置信賴。

* 1994年：當John Peterson注冊了haskell.org的域名并在Yale架設了服務器和站點，Haskell
獲得了Intenet的入場券。（直到今日Hudak的小組仍然維護haskell.org的服務器）

* 1996年5月：1.3版的《Haskell報告》出爐，由Hammond和Peterson編輯。從技術變化的層面來講，
Haskell 1.3是從1.0以來進展最大的Haskell的發布版。特別是：
o 增加了庫（Library）報告，這反映了如下事實—程序很難可移植，除非它們依賴於標準庫
（standard libraries）。
o Monadic I/O首次露面，包括了“do”語法（第7節），并且附錄中的I/O語義被刪掉了。
（drop是刪除？核實之）
o 類型類被泛化到更高的種類（higher kinds）—稱為“構造子類”（constructor classes）
（參見第6節）。
o 通過幾種方式擴展了代數數據類型：新類型（newtypes）、嚴格化注記
（strictness annotations）以及命名字段（named fields）。

* 1997年4月：1.4版的《Haskell報告》發表（139頁+73頁），由Peterson和Hammond編輯。
這個版本是1.3版報告的精簡版；唯一重要的變化是列表內涵（list comprehensions）被泛化到
任意的monads，這一決定兩年后又被回退回來。

* 1999年2月：《Haskell 98報告：語言與庫》發表（150頁 + 89頁），由Peyton Jones和Hughes
編輯。如我們在3.7節描述的，這是一個重要的時刻，因為這表示了對穩定性的承諾。列表內涵被回退到
僅僅列表之上。

* 1999年—2002年：Haskell委員會本身中止了存在，Peyton Jones承擔了唯一的編輯職位，
打算來收集和修正排版上的錯誤。決策已不再限於一個小的委員會，現在任何閱讀Haskell郵件列表的人
都可以參與。

然而，由于Haskell使用得愈來愈廣泛（部分因為Haskell98標準的存在），許多小的語言設計中的缺陷
浮現出來，并且很多報告中的模糊不清之處也被發現。Peyton Jones的角色就演變成了語言瑣事的
仁慈大君（Benign Dictator of Linguistic Minutiae）。

* 2002年12月：《修訂的Haskell 98報告：語言與庫》出版（260頁），由Peyton Jones編輯。
劍橋大學出版社慷慨的把該報告作為一本書來出版，同時允許全文仍能在網上獲得，并且可以被任何人
自由使用。他們應允在這樣不常見的條款下出版一本書，所表現的靈活性極大的有益於Haskell社群，
并和緩了關于自由與知識產權的棘手爭議。

值得注意的是，盡管至Haskell 98出爐時Haskell已經存在了至少8年，但從Haskell 98的首次出版
到“试航”（shake down，“检验船或飞行器性能并使机组人员熟悉其运行”）規范還是花了四年時間。
語言設計是一個漫長的過程！

圖二用圖形方式給出了Haskell的時間線索。圖中的許多實現、庫和工具將在論文的后面討論。

圖略

##### 2.6 Haskell是個玩笑？

Haskell報告的首個版本發表在1990年4月1日。它出現在愚人節主要還是一個意外—一個不得不選擇的
日期，并且版本發布足夠接近於4月1日使使用該日期獲得了充足的理由。
（It was mostly an accident that it appeared on April Fool’s Day—a date had to
be chosen, and the release was close enough to April 1 to justify using
that date.）當然Haskell不是一個玩笑，但版本發布真的導致了一系列愚人節的笑話。

事情的開端還得從Haskell開發的那個瘋狂的年份開始，當時Hudak作為報告編輯的角色尤為壓抑。
在第一年或者第二年的4月1日，他發出了一封email訊息給Haskell委員會，說他已經受夠了，
并且將從委員會辭職，也將離開Yale去音樂界尋求一份職位。委員會的許多成員都相信了那套鬼話，
David Wise立刻給Hudak電話，懇請他從新考慮他的決定。

當然這不過是愚人節的笑談，但是使眾人跟隨的種子已經播下。笑話中的多數都可以在Haskell網站上的
haskell.org/humor獲得細節，這里僅僅是一些最有意思故事的摘要

1.1993年4月1日，Will Partain寫了一份非常出色的宣告，說的是稱為Haskerl的一個Haskell的
擴展，該擴展結合了Haskell中最好的想法與Perl中最好的想法。它技術上如此細致并且又有十分嚴肅
的腔調，這使得它很可信服。

2. 幾個Partain惡搞佳文的回應也頗有趣，并也在4月1日發表。其中一個是Hudak寫的，他寫道：

“最近Haskell在Yale的醫學院投入實驗。它被用來替代一個C程序控制一個心肺機。在投入使用的6個月里，
院方估計成打的生命獲得了挽救，因為這個程序遠比C程序穩健，而后者常常崩潰并置患者於死地。”

作為回應，Nikhil寫道：

“近日，一家本地醫院因不完善的X光機軟件遭到多起醫療事故起訴。因此，為提高可靠性他們決定用
Haskell來編碼重寫。”

“醫療事故訴訟降至為零。原因是他們沒有再購置新的X光機
（我們依然在編譯標準前導庫（Standard Prelude））”

3. 1998年4月1日，John Peterson寫了一份假冒的新聞稿，其中宣稱因為Sun Microsystems已經在
Java的使用上起訴了微軟，微軟已經決定采用Haskell作為它主要的軟件開發語言。
具有諷刺意味的是，這份新聞稿之后不久，Peyton Jones宣布他將從Glasgow轉往Cambridge的
微軟研究院，這件事Peterson在那時一無所知。

接下來的事件讓Peterson的玩笑愈發具有預言性。微軟確實通過支持另一種語言來回擊Java，
但它是C#，而不是Haskell。但C#的許多特性都是由Haskell或者其他函數式語言首創的，
特別是多態類型和LINQ（Language Integrated Query）。Erik Meijer，LINQ的主設計師，
聲稱LINQ直接受啟發於Haskell中的monad內涵（monad comprehensions）。

4. 2002年4月1日，Peterson寫了另外一篇假冒的但有趣且看似真實的文章，標題是“計算機科學家
揭底（bottom）金融醜聞”。這篇文章描述了Peyton Jones，如何通過他的關于利用Haskell形式的
估值金融契約的研究（Peyton Jones et al., 2000），能夠澄清安然（Enron）破舊且搖晃不堪的
金融網絡。Peyton Jones被引用道：

“這實際上很簡單。如果我寫了一份契約說它的價值源自一個股票價格，而股票的價值又唯一的依賴於
这份契约，我们得到了不可計算狀態（bottom）。这样最后，安然创建了一系列复杂的契約，
而这些契約最終全然没有價值。”

譯注：bottom這里是雙關語—在日常的英語中bottom有查明真相、起因的含義；而在計算機科學中，
bottom是指程序進入異常或者不可終止狀態。



