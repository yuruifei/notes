---
title: c++学习笔记（尹成视频）2016-11-17 
tags: c++,笔记
grammar_cjkRuby: true
grammar_abbr: true
grammar_table: true
grammar_defList: true
grammar_emoji: true
grammar_footnote: true
grammar_ins: true
grammar_mark: true
grammar_sub: true
grammar_sup: true
grammar_checkbox: true
grammar_mathjax: true
grammar_flow: true
grammar_sequence: true
grammar_plot: true
grammar_code: true
grammar_highlight: true
grammar_html: true
grammar_linkify: true
grammar_typographer: true
grammar_video: true
grammar_audio: true
grammar_attachment: true
grammar_mermaid: true
grammar_classy: true
grammar_cjkEmphasis: true
grammar_cjkRuby: true
grammar_center: true
grammar_align: true
grammar_tableExtra: true
---


# 目录

[toc]
# 1.valist实现可变参数模板

## 1.1类型一致的情况
```cpp
include<iostream>
include<cstdarg>
using namespace std;

template<typename T>
T add(int n, T t...) {
	va_list arg_ptr; //开头指针
	va_start(art_ptr, n); //从arg_prt开始读取n个
	T res(0);
	for (int i = 0; i != n; ++i) {
		res += va_arg(arg_prt, T);
	}
	va_end(arg_ptr);
	return res;
}

void main() {
	cout << add(1, 4, 2, 3, 4) << endl;
	cout << add(1.2, 3.45, 1.3) << endl;
}
```
## 1.2类型不一致的情况

```cpp
include<iostream>
include<cstdarg>
using namespace std;

void show () {}; //用于结束递归
template<typename T, typename...Args>
void show(T t, Args...args) {
	cout << t <<endl;
	show(args...);
}

void main() {
	show(1, 1.2, "abc");
}
```
## 1.3实现printf

```cpp
# include<iostream>
# include<cstdarg>
using namespace std;

void show(const char *str) {
	cout << str;
}
template<typename T, typename...Args>
void show (const char *str, T t, Args...args) {
	while (str && *str) {
		if (*str=='%' && *(str+1) != '%') {  //这里其实有问题，str+1 没判断是否为null
			str++;
			cout << t;
			show(++str, args...);
			retrun;
		} else {
			cout << *str++;
		}
	}
}
```
# 2. auto
typeid() 是一个操作符
```cpp
void main() {
	auto a = 10;
	auto b = L"bobo";
	cout << typeid(a).name << endl << typeid(b).name << endl;
}
```
auto 自动推理数据类型
```cpp
template<typename T1, typename T2>
auto add(T1 t1, T2 t2) {
	return t1 + t2;
}

void main() {
	cout << add(10 + 12.3) << endl;
	cout << add(1.1 + 30) << endl;
}
```
```cpp
auto (*f)() -> int (*)(); //f是什么？ f是一个函数指针，它的返回值是一个函数指针
int (*(*fx)())();

auto pf() -> auto(*) (int x) ->int (*)(int a, int b) {
	return nullptr;
}

```
auto无法区分常量和变量，无法区别变量和引用
auto无法放在结构体内部
typeid只能获取类型，引用、常量属性无法获取

# 3. decltype
```cpp
void add(int a) {
	cout << a << endl;
}
void main() {
	auto afu(1 && 2);
	decltype(afu) afu1[5] {0};//decltype 根据变量或表达式获取数据类型
	for (auto i : afu1) {
		cout << typid(i).name << ":" << i << endl;
	}
	
	decltype(add) add1 = add;   //ok 生成一个函数类型
	decltype(add) *add2 = add;  
	decltype(add) &add3 = add; 
	add1(1);  //err
	add2(1);  // ok
	add3(1);   //ok
}
```
decltype 可以获取常量属性，可以识别引用属性
# 4. c++与c区别
## 4.1. 初始化
```cpp
int *p1 = new int[5] {1, 2, 3, 4, 5};  //c风格
int *p2(new int[5] {1, 2, 3, 4, 5}); //c++风格
int a[5] {1, 2, 3, 4, 5}; //c++初始化
```
## 4.2. 数组
```cpp
int *p = (int[]) {1, 2, 3, 4, 5};  //c99 语法，在c++下无法编译通过
int *p = (int[20]) {[4] = 1, [5] = 2}  //c99 语法，在c++下无法编译通过
array<int, 10> myint{1,2,3,4,5,6,7,8,9,10}; //cpp风格数组
```
## 4.3. nullptr
```cpp
void show(int num) {
	cout << "int" << endl;
}
void show(int *p) {
	cout << "int *" << endl;
}
void main () {
	show(NULL); //c风格的控指针会被误认为int
	show(nullptr);
	cout << typeid(NULL) .name<< endl;
	cout << typeid(nullptr) .name<< endl; 
}
```
## 4.4. using 与 typedef
```cpp
typedef double DB;
using DBCPP = double;

typedef int a[10];
using intarry = int[10];

typedef int(*p) (int a, int b);
using pfun = int(*) (int a, int b);

typedef int(*pa[10]) (int a, int b);
using pfun = int(*[10]) (int a, int b);
```

## 4.5. 收缩类型转换

```cpp
void main () {
	char ch(7800);   //ok，但是会丢失数据
	char ch1{7800};  //err,编译不通过，收缩转换时尽量是用大括号初始化。
}
```
## 4.6 const的区别
`
```cpp
const int n = 10;
int a[n]; //ok cpp编译器自动优化
int k = 10;
const int m = k;
int b[m]; //err cpp编译器不自动优化
```
```cpp
const int n = 10;
*(int*)(&n) = 3;
cout << n << endl;  // cpp输出10，c输出3；

int a = 10;
const int m = a;
*(int*)(&m) = 3;
cout << m << endl; //cpp输出3；
```
## 4.7左值与右值的优化
在c语言中
```cpp
int i = 3;   
(++i) ++；  //err
(i = 3) = 4;   //err
```
在cpp中,会把有内存实体的右值转换为左值

```cpp
cpp
int i = 3;   
(++i) ++；  //ok
(i = 3) = 4;   //ok
i++ = 2;  //err
```

## 4.8 union的区别

c语言中
```cpp
union mu {
	int num;
	double data;
	void(*p);
};

```
在cpp中，可以初始化，可以有函数，共享内存，不计入代码区大小
```cpp
union mu {     //sizeof ： 8
	int num = 10;
	void go  () {
		cout << "aaaa" << endl;
	}
private:  //可以有private  但不可以继承
    ...
};

mu *p(nullprt); // ok    定义变量不需要union关键字
p->go();  
```

## 4.9 struct

 - cpp结构体可以为空，c不可以
 - cpp结构体成员可以有默认值，c不可以
 - cpp结构体定义变量无需关键字struct，c必须有
 - cpp结构体可以有函数
 - cpp结构体成员变量可以是函数指针、变量、lamda
	 - 函数包装器占40个字节
	 - 指针占4个字节
	 - lamda语句块在代码区，本身不计入空间
 -  cpp结构体中没有私有变量的情况下，初始化可以用大括号
 -  结构体可以声明，struct MyStruct;  // 声明后可以创建指针。
 -  匿名结构体，成员变量不能有默认值
```cpp
struct MyStruct {
	void(*p) () = [] () {};
	function<void(void)> fun = [] () {} ;
};
cout << sizeof(Mystruct) << endl;  // 44  (in x86) 

struct ms {
	int x;
	int y;
};
ms ms1{2, 3};  // ok
```

# 5. constexpr
标识返回值或其他表达式是常量
```cpp
const expr int get() {
	return 5;
}
void main() {
	int a[5 + get ()];  // ok 可以识别为常量
}
```

# 6. inlinenamespace
```cpp
namespace all {
	inline namespace 2016 {  //all::x默认调用这个
		int x;
	}
	namespace 2015 {
		int x;
	}
}
```
# 7. lamda
```cpp
void main() {
	auto fun = [] {cout << "hello world"<<endl;};  	
	fun();
	[] {cout << "hello world new"<<endl;}();  //匿名lamda表达式
	[](char *str) {cout << "hello" << str << endl;}("fangfang");
	auto fun2 = [](double a, int b)->decltype(a+b) {return a + b};
	
	int num1 = 100;
	int num2 = 99;
	[](){cout << num1 << num2 << endl;}; // err无法读取外部变量
	[=](){cout << num1 << num2 << endl;};  //ok  读取外部变量
	[&](){num1 = 1; num2 = 2; cout << num1 << num2 << endl;};  //ok 修改外部变量
	[=](){num1 = 1; num2 = 2; cout << num1 << num2 << endl;};  //err 无法改变外部变量  
	[=]() mutable {num1 = 1; num2 = 2; cout << num1 << num2 << endl;};  //ok   修改副本
	[&num1, num2](){num1 = 1; cout << num1 << num2 << endl;}; //ok num1可写，num2 可读
}
```
递归
```cpp
function<void(void) func = [&] () { cout << "stack overflow" ; func(); };  //必须有&，语法上看func 是外部变量
func()
```
# 8. 函数包装器
```cpp
#include<iostream>
#include<functional>

using namespace std;
using std::function; 
void go() {
	cout << "go here" << endl;
}
void main() {
	function<void(void)> fun1 = go;
}
```
# 9. 模板元
## 9.1决递归加速
```cpp
//本例适用于cpp11，cpp14又有变化
template<int N>
struct data {
	enum {res = data<N - 1>::res + data<N - 2>::res};
};
template<>
struct data<1> {
	enum {res = 1};
};
struct data<2> {
	enum {res = 2};
};

void main() {
	cout << data<50>::res << endl;
}

```
## 9.2 模板参数展开
```cpp
template<class T>
void show(T t) {
	cout << t << endl;
}
template<class...Args>
void all(Args...args){
	int arr[] = { (show(args), 0)... }
}
void main() {
	all(1, 2.3, 'a');
}
```
# 10. tuple
```cpp
#include<iostream>
#include<tuple>

void main () {
	char ch('x');
	int num(1);
	double db(3.3);
	tuple<char, int, double> mytp(ch, num, db);
	auto first = get<0>(mytp);
	int i = 0;
	auto first1 = get<i>(mytp);  //err get<>不能使用变量。
	for (auto i : mytp) { //err 注意！ tuple不能使用auto遍历
	}
}
```

# 11. 引用
## 11.1. 左值引用与右值引用
```cpp
int num (1);
int & lrnum(num);  //左值引用
int && rrnum(num + 4); //右值引用，快速备份， 编译器自动回收  
					  // num + 4 本身是无法去地址的 &(num + 4) 不合法
cout << num << endl;
int && rrnum1(move(num));  //右值， 有内存实体就直接引用， 没有则开辟内存
rrnum1 = 2;
cout << num << endl;
cout << rrnum << endl;
```
```cpp
void show(int && rrnum) {
	cout << rrnum << endl;
}
void main () {
	int a[5]  {1, 2, 3, 4, 5};
	show(a[3] + 2);
	show(a[3]); // err， 无法将左值转换为右值
	show(move(a[3]));  // ok 移动语义
}
```
## 11.2 指针的引用
```cpp
int a[5] {1, 2, 3, 4, 5};
int *p(a);
cout << *p << endl; // 1
int * & rp(p);  //左值引用改变指针。  & 要放在类型与变量名之间
rp += 1;
cout << *p << endl; // 2

int * && rrp(p + 2);  
cout  << *rrp << endl;  //  3
rrp += 2;
cout << *rrp << endl;  // 5

int * && rrp1(&a[1]);
cout << *rrp << endl;  // 2
```

## 11.3 本质
引用本质上是用指针实现的

## 11.4. 数组的引用
```cpp
int a[5] {1, 2, 3, 4, 5};
int *pa[5] {a, a+1, a+2, a+3, a+4};
int b[3][4] {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12};
int *p1(new int[5] { 1, 2, 3 ,4 ,5});
int (*p2)[4] = new int[3][4] {1, 2, 3, 4},{ 5, 6, 7, 8}, {9, 10, 11, 12};

int (&ra)[5](a);   //数组的引用
int * (&rpa)[5](pa);
```
## 11.5 元素是引用的数组
引用数组是非法的。

## 11.6. const 与引用
```cpp
int *p(new int(5));
int *&rp(p);
*rp = 3; //ok

const int *&crp(p);  //err 无法从int * 转化为 const int *&
const int *cp(new int(5));
const int *& crp1(cp); // ok
```
## 11.7 右值引用补充
### 11.7.1 右值引用的引入
右值引用（及其支持的Move语意和完美转发）是C\++0x加入的最重大语言特性之一。从实践角度讲，它能够完美解决C\++中长久以来为人所诟病的临时对象效率问题。从语言本身讲，它健全了C\++中的引用类型在左值右值方面的缺陷。从库设计者的角度讲，它给库设计者又带来了一把利器。从库使用者的角度讲，不动一兵一卒便可以获得“免费的”效率提升。在标准C\++语言中，临时量（术语为右值，因其出现在赋值表达式的右边）可以被传给函数，但只能被接受为const &类型。这样函数便无法区分传给const &的是真实的右值还是常规变量。而且，由于类型为const &，函数也无法改变所传对象的值。C\++0x将增加一种名为右值引用的新的引用类型，记作typename &&。这种类型可以被接受为非const值，从而允许改变其值。这种改变将允许某些对象创建转移语义。比如，一个std::vector，就其内部实现而言，是一个C式数组的封装。如果需要创建vector临时量或者从函数中返回vector，那就只能通过创建一个新的vector并拷贝所有存于右值中的数据来存储数据。之后这个临时的vector则会被销毁，同时删除其包含的数据。有了右值引用，一个参数为指向某个vector的右值引用的std::vector的转移构造器就能够简单地将该右值中C式数组的指针复制到新的vector，然后将该右值清空。这里没有数组拷贝，并且销毁被清空的右值也不会销毁保存数据的内存。返回vector的函数现在只需要返回一个std::vector<>&&。如果vector没有转移构造器，那么结果会像以前一样：用std::vector<> &参数调用它的拷贝构造器。如果vector确实具有转移构造器，那么转移构造器就会被调用，从而避免大量的内存分配。

### 11.7.2 左值与右值的区别
  一个左值表达式代表的是对象本身，而右值表达式代表的是对象的值；变量也是左值。
### 11.7.3 右值引用绑定的对象

 - 返回非引用类型的函数，
 - 产生右值的表达式（算术表达式、关系表达式、位、后置递增递减）
### 11.7.4 和左值引用的区别
 - 绑定的对象（引用的对象）不同，左值引用绑定的是返回左值引用的函数、赋值、下标、解引用、前置递增递减
 - 左值持久，右值短暂，右值只能绑定到==临时对象==，所引用的对象将要销毁或该对象没有其他用户
 - 使用右值引用的代码可以自由的接管所引用对象的内容
 ```cpp
int &&i = 1;
int b = 2;
cout << i << endl;
i = b;
cout << i << endl;
//输出1 2
//初始化时，右值引用一定要用一个右值表达式绑定；初始化之后，可以用左值表达式修改右值引用的所引用临时对象的值
 ```

# 12 多线程
## 12.1 多线程及传参
```cpp
#include<thread>
#include<iostream>
using namespace std;

void run(int i) {
	cout << "haha" << i << endl;
}
void main() {
	thread p(run, 1);
	thread *q(new thread(run, 2);
	cin.get();
}
```
## 12.2 join 和 detach
join用于等待线程返回
```cpp
void show () {
	//do something
}
void main() {
	int n = thread::hardware_concurrency();  //获取CPU核心数目
	array<thread, 3> threads {thread(show), thread(show), thread(show),};
	//for (auto i : threads) // err,编译错误  需要用 for(auto & i : threads)
	for (int i = 0; i < 3 ++i) {
		threads[i].join();
	}
}
```
detach 脱离主线程，主线程退出不影响子线程。
```cpp
void show () {
	//do something
}
void main() {
	thread th(show);
	th.detach()  // 孤儿线程
	cout << th.joinable() << endl;  //false	
}
```

## 12.3 原子变量与互斥
```cpp
#include<iostream>
#include<thread>
#include<mutex>
#include<atomic>
using namespace std;
int num = 0;
atomic_int num1{0}; // 原子变量
mutex m;
void run() {
	for (int i = 0; i < 10000000; i++) {
		m.lock();  //互斥量的使用，反复加锁解锁浪费时间
		num++;
		m.unlock();
	}
}
void run1() {
	for (int i = 0; i < 10000000; i++) {
		num1++; //原子变量速度比mutex 快
	}
}
void main() {
	clock_t start = clock();
	thread t1(run);
	thread t2(run);
	th1.join();
	th2.join();
	clock_t end = clock();
	cout << end - start << "ms" << endl;
	thread t1(run);
	thread t2(run);
}

```
## 12.4 lamda与多线程
```cpp
auto fun = [] () {cout << "lamda" << endl;};
thread th1(fun);
thread th2(fun);
thread th3([] () {
	cout << this_thread::get_id() << endl;
	this_thread::sleep_for(chrono::seconds(3));  //include<chrono>
	this_thread::yield(); //让cpu先执行其他线程，空闲时在执行我
	//this_thread::sleep_until(); 
});
```
## 12.5 伪函数与多线程
伪函数可以将对象当作函数名使用
```cpp
#include<iostream>
#include<thread>
using namespace  std;
struct mystruct {

	mystruct() {
		cout << "create" << enld;
	}
	~mystruct() {
		cout << "delete" << endl;
	}
	void operator() () {
		cout << "abcdefg" << endl;
	}
};
void main() {
	mystruct go1;
	thread t1(go1);
	mystruct go2;
	thread t2(go2);
	mystruct()(); //匿名对象调用operator();
	thread t3(mystruct()); //err 匿名对象不适合作为多线程参数，有可能销毁太快 。
	cin.get();
}
```
## 12.6 成员函数与多线程

```cpp
#include<iostream>
#include<thread>
using namespace std;

struct fun {
	void run() {
		cout << "hello" << endl;
	}
};
void main() {
	fun fun1;
	thread th(&fun::run,fun1);  //&fun::run引用成员函数
	thread th1(&fun::run,fun1);
}
```
## 12.7 多线程通信futrue与promise todo

```cpp
#include<future>
#include<iostream>
#include<string>
#include<thread>
#include<cstdlib>

promise<string> val; //全局通信变量
void main() {
	thread th1 ( [] () {
		futrue<string> fu = val.get_future();
		cout << "等待中" << endl;
		cout << fu.get() << endl;
	});
	thread th2 ( [] {
		system("pause");
		val.set_value("fangfang ");
	});
	th1.join();
	th2.join();
}

```
## 12.8 线程条件变量 todo
三个线程id分别为A B C 每个线程打印自己的id10次，需要按照ABC的顺序打印
```cpp
#include<iostream>
#include<thread>
#include<mutex>
#include<ondition_variable>
using namespace std;
int LOOP = 10;
int flag - 10;
mutex m;
condition_variable ca;
void fun(int id){
	for (int i = 0; i < LOOP; i++) {
		unique_lock<mutex> ulk(m);
		while( id - 65 != flag) {
			cv.wait(ulk);
		}
		cout << (char)id << endl;
		flag = (flag + 1) % 3;
		cv.notify_all();
	}
}
void main() {
	thread t1(fun, 65);
	thread t2(fun, 66);
	thread t3(fun, 67);
	t1.join()
	t2.join();
	t3.join();
	cin.get();
}
```

```cpp
//mlgb 完全看不懂
#include<thread>
#include<iostream>
#include<mutex>
#include<condition_variable>
using namespace std;
mutex m;
condition_variable cv;
void main() {
	thread **th = new thread* [10];
	for (int i = 0; i < 10; i++) {
		th[i] = new thread([] (int index) {
			unique_lock<mutex> lck(m);  //锁定当前线程
			cv.wait_for(lck, chrono::hours(1000));
			cout << index << endl;
		}, i);
		this_thread::sleep_for(chrono::milliseconds(100));
	}
	for (int i = 0; i < 10; i++) {
		lock_guard<mutex> lckg(m); //解锁线程
		cv.notify_one();
	}
	//cv.notify_all();
	for (int i = 0; i < 10; i++) {
		th[i]->join();
		delete th[i];
	}
	delete [] th;
	cin .get();
}
```

## 12.9 使用继承拓展多线程

```cpp
#include<iostream>
#include<thread>
using namespace std;

class Huathread : pulic thread {
pulic:
	Huathread() : thread() {
	//构造函数
	}
	template<typename T, typename...Args>
	Huathread(T && func, Args &&...args) 
		: thread(forward<T>(func), forward<Args>(args)...) {
	
	}
	void run(const char* cmd) {
		system(cmd);
	}
};
void main() {
	Huathread t1( [] () { cout << "hello, this is hua" << endl;});
	t1.run("calc");
	Huathread t2( [] (int num) { cout << "hello, this is hua" << num << endl;});
	t2.run("notepad");
}
```
## 12.10 mutex
```cpp
#include<thread>
#include<iostream>
#include<mutex>
using namespace std;
#define N 100000;
mutex g_mtx;
void add (int *p) {
	for (int i = 0; i < N; i ++) {
		lock_guard<mutex> lgd(g_mtx);   //拥有mutex所有权，读取失败的情况就一致等待，自动加锁，自动解锁
		//unique_lock<mutex> ulk(g_mtx); //作用同上，自动加解锁，根据g_mtx的值来决定是否可以访问，根据块语句锁定
		(*p)++;
	}
}
void main() {
	int a = 0;
	thread t1(add, &a);
	thread t2(add, &a);
	t1.join();
	t2.join();
	cout << a << endl;
	cin.get();
}
```
## 12.11 线程交换和移动

swap
```cpp
thread t1(fun);
thread t2(fun);
swap(t1, t2);  //用于线程负载过重时
```
move
```cpp
thread t1(fun);
thread t2 = move(t1);
cout << t1.get_id() << endl;  // 0 
cout << t2.get_id() << endl;
```

# 13 new delete  和 malloc free
## 13.1 基本数据类型和复合数据类型
对于基本数据类型,delete 和 free 都会释放，释放两次都会出错，free不改变指针的值， delete会改变。
对于类类型new和delete自动调用构造和析构函数。

```cpp
int *p = new int(5);
delete p;  // 必须加上 p = nullptr;
delete p; // 会出错，迷途指针
```
对于基本数据类型 delete 和 delete[] 可以混用。
对于复合数据类型数组必须对应delete[]，单个变量必须对应delete。
## 13.2 在栈区用new分配空间
new 可以指定分配内存的地址
```cpp
char *str[1024] = {0};
int *p = new(str) int[10] {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}; // 指定从str开始分配，如果str时全局变量，则p分配在静态区，若是局部变量，则p分配在栈区，若str在堆上，则p分配在堆区。
delete p; // 静态区和栈区只能覆盖重写，不能delete。 堆区可以覆盖也可以delete.
```
## 13.3 重载new delete

```cpp
class myclass {
public:
	myclass() {
		cout << "create" << endl;
	}
	~myclass() {
		cout << "delete" << endl;
	}
	//针对new做出一种新的解释，只对当前类生效
	//重载new可以实现单例模式
	//每个类都有一个new 和 delete
	static void* operator new () (size_t size) {
		myclass *p = ::new myclass;
		return p;
	}
	//重载delete可以避免重复delete产生的错误
	static void operator delete(void *p) {
		::delete p;
	}
};
``` 
## 13.4 初始化的顺序
构造： new -> ::new -> malloc -> 构造函数
析构：析构 ->delete -> ::delete -> free
# 14 类的内存管理
## 14.1 类的大小
sizeof() 计算类大小时，空类占一个字节，表示它的存在
其他情况和struct类似，不会统计函数。
原因是对于一个类的不同对象，数据成员是独有的，但是函数成员是共享的。
```cpp
class myclass {
public:
	void show () {
		cout << "myclass show" << endl;
	}
};
void main() {
	myclass *p(nullptr);
	p->show(); // ok 因为show没有用到对象的数据成员，所以可以调用。
}
```

# 15 时间 
```cpp
#include<chrono>
#include<ctime>
using namespace std;

void main() {
	double db = 0;
	time_t t1, t2;
	t1 = time(&t1);
	auto start = chrono::high_resolution_clock::now();
	for (int i = 0; i < 100000000; i++) {
		db += i;
	}
	t2 = time(&t2);
	auto end = chrono::high_resolution_clock::now();
	cout << difftime(t2, t1) << endl;
	cout << (end - start).count() <<endl;  //单位是 10e-9 秒
	cin.get();
}
```
# 16 生产者消费者模型

# 17 类型转换
static_cast  基本数据类型
reinterpret_cast   指针
const_cast  
dynamic_cast  父类和子类之间，若不匹配返回nullptr。主要用于多态，父类必须有虚函数。

```cpp
static_cast<double> (10);

int * pi = new int(1);
char *pch = reinterpret_cast<char*>(pi);
for (int i = 0; i < 4; i++) {
	cout << *(pch + i)  << "--"
	<< static_cast<int>(*(pch + i)) << "--"
	<< reinterpret_cast<void*>(pch + i) //打印地址
	<< endl;  
}

int num[5] ={1, 2, 3, 4, 5};
const int *p = num;
int *pint = p; // err
int *pint = const_cast<int*>(p);
```
dynamic_cast 只能用于指针或引用，只能用于父类指针转化为子类指针。

一个父类指针被赋值子类对象地址，父类有虚函数，则通过多态可以被识别为子类对象，若无虚函数，则无法识别为子类对象 。

父类指针可以根据多态转化为子类指针，对于一个父类指针：
识别为父类对象，转换失败，返回0000
识别为子类对象，转换成功
dynamic_cast 基于虚函数转换，若无虚函数则不能被识别为子类对象，所以转换失败

```cpp
class huahua{
public:
	int hua1;
	virtual void run() {
		cout << "huahua  is running" << endl;
	}	
};
class xiaohua  : public huahua {
public:
	int  xiaohua1;
	void run() {
		cout << "xiaohua is playing" << endl;
	}
};
void main() {
	huahua * pfu = new huahua;
	xiaohua *pzi = new xiaohua;
	huahua *phua = dynamic_cast<huahua*>(pzi); // ok
	xiaohua *pxiaohua = dynamic_cast<xiaohua*>(pfu);
	if (pxiaohua != nullptr) {
		pxiaohua->hua1 = 10;
		pxiaohua->xiaohua1 = 10;      //可能出错
		pxiaohua->run();   
		(*pxiaohua).huahua::run*();//子类调用父类中的方法
	}
}
```
# 18 delete 与函数声明
函数声明后加 = delete ，该函数不再可用，可用于构造、析构、赋值等函数
```cpp
void show(int num) = delete;

class myclass {
public:
	myclass() = delete;  无法创建
	~myclass() = delete;  无法销毁
};
```

# 19 大括号语法initializer_list
```cpp
#include<initializer_list>
#include<iostream>
using namespace std;
void show(initializer_list<int> list) {
	for (auto i : list) {
		cout << i << endl;
	}
}
void main(){
	show( {1, 2, 3, 4, 5} );
}
```

# 20 STL
## 20.1 forward_list
```cpp
#include<forward_list>
using namespace std;
void main() {
forward_list<int> list1 {1, 2, 3, 4, 5};
list1.push_front(10);
list1.push_back(10); //err 不能pushback
auto it = list1.before_begin();  //头指针，无数据
list1.insert_after(ib, 19);  //再最前面插入
list1.size() // err！forward_list 时唯一没有size() 的标准容器

}
```
## 20.2 algorithm
108个算法
```cpp
#include<algorithm>
struct ms {
	bool operator () (int data) {
		return data % 2 == 1;
	}
};
bool get(int data) {
	return data % 2 == 1;
}

void main() {
	vector<int>vi{ 1, 2, 3, 4, 5, 6, 7, 8, 9 };
	int num = count_if(vi.begin(), vi.end(), [](int data){ return data % 2 == 1;});
	int num1 = count_if(vi.begin(), vi.end(),ms() ); //匿名对象
	
	ms ms1;
	int num2 = count_if(vi.begin(), vi.end(), ms1); 
	
	int num3 = count_if(vi.begin(), vi.end(), get); 
	
	ms my1;
	auto fun = bind(&my::get, &my, _1);  // <placeholders>
	int num4 = count_if(vi.begin(), vi.end(), fun); 
	
}

```

# 21 ifexists
测试变量、函数或类型是否存在
```cpp
int num;
void run() {
}
class myclass() {
};
__if_exists(num) {
	cout << "num exists" << endl;
}
__if_not_exists(run) {
	cout << "not exists" << endl;
}
```

# 22 templae

## 22.1 引用包装器
```cpp
template<typename T>
void show(T t) {
	cout << t << endl;
	t += 10;
}

void main() {
	double db(1.0);
	double & rdb(db);
	show(db);   
	show(rdb);  //引用传给模板不会被识别为引用
	cout << db << endl;   // 1
	show(ref(rdb));   //ref 引用包装器
	cout << db << endl; // 11
}
```
## 22.2 函数包装器
```cpp
#include<functional>  //函数包装器
#include<iostream>
using namespace std;
using std::function;

int add(int a, int b) {
	return a + b;
}
template<class T, class F>
T run(T t1, T t2, F f) {
	return f(t1, t2);
}

void mian(){
	function<int(int, int)> fun1 = add;
	function<int(int, int)> fun2 = [](int a, int b)->int { return a - b;};
	cout << run(19, 29, fun1) << endl;
	cout << run<int, function<int(int, int)> >(19, 29, fun1) << endl;
}
```

## 22.3 模板内部的引用todo

```cpp
template<class T>
void print(T t) {
	cout << "void print(T t)" << endl;
	t += 1;
	cout << t << endl;
}
template<class T>
void print(T & t) {
	cout << "void print(T & t)" << endl;
	t += 1;
	cout << t << endl;
}
template<class T>
void print(T && t) {
	cout << "void print(T && t)" << endl;
	t += 1;
	cout << t << endl;
}
void main() {
	int data(100);
	int & rdata(data)
	int && rrdata(data + 1）；
	print(data) // err 重载不明确
	print<int>(data) // err 不明确
	print<int &>(rdata) // err 不明确
	print<int &&>(data) // ok 
}
```

## 22.4 函数模板指针参数重载 todo
有指针有限匹配指针，尽量匹配星号多的，即多级指针。

## 22.5 函数指针与函数模板
```cpp
template<class T>
void show(T t) {
	cout << t << endl;
}
void main() {
	void (*p)( int i ) = show<int>;  
}
```

 
# 23 函数绑定器
## 23.1包装类成员函数，便于调用
```cpp
#include<iostream>
#include<functional>
using namespace std;
using namespace std::placeholders;

struct MyStruct {
	void add (int a) {
		cout << a << endl;
	}
};
void main () {
	MyStruct my1;
	my1.add(10);
	cout << typeid(my1.add).name() << endl;  // void __cdecl(int)
	cout << typeid(&MyStruct::add).name() << endl;  
							// void (__thiscall MyStruct::*)(tint)
	void (MyStruct::*p) (int a) = &MyStruct::add;
	MyStruct *pmy1 = &my1;
	(my1.*p)(1);  //ok
	(pmy1->*)(1);  //.* 和 ->* 也是类运算符
	auto fun1 = bind(&MyStruct::add, &my1, _1)  // _1代表有一个参数
	//两个参数：auto fun1 = bind(&MyStruct::add, &my1, _1, _2) 
	fun1(1000); // ok 
	cin.get();
}

```
## 23.2 绑定函数、lamda和伪函数
```cpp
#include<iostream>
#include<functional>
using namespace std;
using namespace std::placeholders;
int add(int a, int b) {
	return a + b;
}
struct MyStruct {
	int operator()(int a, int b) {
		return a + b; 
	}
};

void main() {
	auto fun = bind(add, 10, _1); //适配器模式
	cout << fun(1100) << endl;
	
	auto fun1 = bind([] (int a, int b)->int {return a + b}, 100, _1);
	cout << fun1(123) << endl;  // 223
	
	MyStruct my1;
	auto fun2 = bind(my1, 100, _1);  //_1 绑定的时第一个参数 ，即int a
	cout << fun2(123) << endl; // 223
	cin.get();
}
```

# 24 静态断言

## 24.1 c的断言
```cpp
#include<cassert>
assert(a != 0); //debug模式下，运行时若a==0则触发
```
## 24.2 静态断言

```cpp
static_assert(sizeof(void*) >= 8，  "not 64");  //编译时触发，判断是否为64位
int a = 0;
static_assert(a > 0, "haha");  //err  a不能时变量
```

# 25 转义字符

```cpp
#include<string>
#include<cstdlib>
string str("c:\\windows\\system32\\driver");
string str1(R"( "c:\windows\system32\driver" )")  //不需要处理转义字符
string str2 = R"(asdfasdfas)"safdsa)";  //err
string str2 = R"-(asdfasdfas)"safdsa)-";  //ok
```

# 26正则表达式


# 27 typetraits
```cpp
#include<type_traits>

int i(10);
int & ri(i);
int && rri(i+3);
cout << is_lvalue_reference<decltype(ri)>::value << endl;
cout << is_rvalue_reference<decltype(rri)>::value << endl;
int a[5];
int *p = a;
cout << is_array(decltype(a)>::value << endl;  // 1
cout << is_array(decltype(p)>::value << endl; // 0

```
配合模板高级用法
```cpp
template<typename T1, typename T2>
void check_type(const T1 &t1, const T2 &t2, 
		typename enable_if<is_same<T1, T2>::value>::type *p = nullptr) {
	cout << ”类型相同“ << endl;
}
template<typename T1, typename T2>
void check_type(const T1 &t1, const T2 &t2, 
		typename enable_if<!is_same<T1, T2>::value>::type *p = nullptr) {
	cout << ”类型bu相同“ << endl;
}
	
```
# 28 enum
```cpp
enum color {
	red,
	yellow,
	blue,
	white
};
cout << red << endl;  // 0
enum color1(red);
color1 = 3 //err c语言可以， c++ 不行
color1 = blue； // ok

// c++ 风格枚举
enum class jun : int {
	siling = 0,
	junzhang,
	gongbin,
}；
jun jun1(jun::siling);
```

# 29 占位参数
占位参数无法调用，用于预留参数接口。c语言没有占位参数。
```cpp
int add(int a, int b, int) {
	return a + b;
}
void main () {
	add(1, 2, 3); //ok 3比u会产生作用。
}
```

# 30 寄存器变量
register 寄存器变量堆与cpp编译器时一个建议，检测到取地址，会自动优化位内存变量


# 31 异常
```cpp
try {
	throw 1;   //可以是int也可以是 其他类型
}
catch (int n) {
	if (n == 1) {
		cout << "aaaa" << endl;
	}
}
```
# 32 极限

```cpp
#include<limits>
cout << numeric_limits<int>::max() << endl;   //2147483647
cout << numeric_limits<int>::min() << endl;  //-2147483648
cout << numeric_limits<int>::lowest() << endl;  //-2147483648
cout << numeric_limits<double>::max() << endl; //1.79769e+308
cout << numeric_limits<double>::min() << endl;  //2.22507e-308 能表示最小精度的数
cout << numeric_limits<double>::lowest() << endl; //-1.79769e+308 能表示最小的数 
```

# 33 内存分配器< allocator >
手动控制构造和析构的时机。
todo

# 34 类的四大默认函数
```cpp
class myclass {
public:
	myclass() = delete;
	~myclass() = delete;
	myclass(const mycalss & my) = delete;
	myclass operator= (const myclass & my) = delete;
};
```
如果写了构造函数，则c++不会自动生成默认构造函数
```cpp
class myclass{
public:
	int x; 
	int y;
	/*myclass() {
	}*/
	myclass() = defalt;  //defalt  让默认构造函数生效
	myclass(int a, int b) : x(a), y(b) {
	
	}

};
```

# 35 指针数组
```cpp
int *p = new int[10];
int **pp = new int *[10];
int (*px)[4] = (int(*)[4]) new int[20]; 
int *(*py)[4] = int * (*) new int*[20]; 
```
# 36 分数类型
```cpp
#include<ratio>
typedef ratio<1,2> er;
typedef ratio<1,3> san;
cout << er::den << er::num << endl;
ratio_add<er, san> sum;
er er1;
```
# 37 委托构造
```cpp
class fangfang() {
public:
fangfang():fangfang(1,'a'){
	//
}
private:
fangfang(int i, char c) {
	//
}
};
```
# 38 const 与类

 - 成员函数后面加const，限定函数不能改变数据成员。 
 - const对象可以调用const方法，不能调用非const方法。
 - const数据成员在构造时必须初始化。 
 - 可以间接修改const数据成员。
 - const不能用于构造和析构函数，但const对象可以调用他们
 - mutable成员变量不受const影响

# 39 友元

## 39.1 友元函数
```cpp
class myc {
int x;
int y;
friend void show(const myc &myc1)；
};
void show (const myc & myc1) {
	cout << myc1.x << myc2.y << endl;
}
```

## 39.2 友元类
friend  class mynewc;

# 40 explicit
explicit 限定构造函数不能自动转换数据类型，但不能限定强制转换
```cpp
myclass my1(4);   // ok
myclass my2 = 4;  // 不行
```
# 41 final 与 override

 - final 函数不能被重写
 - final 必须用于虚函数
 - override 声明一下我重写了虚函数接口
 - override 会自动检测父类虚函数是否存在，不存在则出错

# 42继承

 - 所有子类对象与父类对象共享父类static成员
 - 重载会被继承
 - 3p与继承
	 - public继承  保持
	 - private继承   败家子
	 - protected继承  守财奴

# 43虚函数

 - 任何一个类，如果有一个或多个虚函数，内部隐藏一个指针，只想虚函数表
 - 构造函数不能是虚函数，若是，子类对象中父类的拷贝无法实现。
 - 使用多态的时候，析构函数必须为虚函数。若不是，则父类指针无法调用子类析构函数，会造成内存泄漏。
 - 继承的本质是包含，子类自动调用父类构造和析构函数。
 - 多态依赖于指针或引用调用，若是对象调用，不能基于对象，若用对象则在传参的时候会产生副本并调用父类函数。
 - 虚函数被继承了还是虚函数。
 - 使用虚函数的时候不要使用重载， c++编译器不是别。
 - 继承时可以显式地通过类名指定方位父类的虚函数。
# 44虚基类与虚继承
问题： b，c 继承与 a ，  c多继承与b和c 则产生二义性，和浪费内存。
解决： 因此有了虚继承
```cpp
class a{

};
class b : virtural pulic a {};
class c : virtural pulic a {};
class c: public b, public c { } ;
```

# 45 纯虚函数与抽象类
纯虚函数为子类提供一个统一接口，
拥有纯虚函数的类成为抽象类，抽象类不能创建对象

hi gitbut



 

# ----------
|First Header  | Second Header | First Header  | Second Header | Third Header|
|------------- | -------------|-------------|-------------|-------------|
表身1Content Cell  | Merge Content Cell|Content Cell  | Content Cell| Content Cell|

表身2Content Cell  | Merge Content Cell|Content Cell  | Content Cell| Content Cell|
[表格标题]


First Header  | Second Header
------------- | -------------
Content Cell  | Content Cell
Content Cell  | Content Cell