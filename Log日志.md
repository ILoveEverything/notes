# Log日志

## main.go

```go
//日志级别：由低到高
//	Debug
//	Info
//	Warning
//	Error
//	Fatal
//完整的日志记录包含：时间、文件名、行号、日志级别、日志信息
func main() {
	//当错误级别是error及以上时打印日志
	log := LogCode.NewLog("warning", "", "test.log", 1024*1024*1024) //1024*1024*1024(byte)字节--> B<K<M<G<T
	for i := 0; i <= 10000; i++ {
		log.Debug("这是一条Debug日志")
		log.Info("这是一条Info日志")
		log.Warning("这是一条Warning日志")
		log.Error("这是一条Error日志")
		log.Fatal("这是一条Fatal日志")
		time.Sleep(time.Second)
	}
}
```

## logCode.go

```go
func init() {
	file, err := os.OpenFile("./test.log", os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)
	//计算文件大小
	//fileInfo, err := file.Stat()
	//fileInfo.Size()
	if err != nil {
		fmt.Println(err)
		return
	} else {
		//将后续的log日志输出写入文件中
		log.SetOutput(file)
	}
}

//自定义log级别
type LogLevel uint

//将log级别定义为常量
//parseLogLevel 对log级别赋予权重
const (
	UNKNOWN = iota //0
	DEBUG          //1
	INFO           //2
	WARNING        //3
	ERROR          //4
	FATAL          //5
)

// Logger log结构体
type Logger struct {
	Level       LogLevel //日志等级的权重
	filePath    string   //日志文件路径
	fileName    string   //日志文件名称
	maxFileSize int      //日志文件大小
}

func parseLogLevel(s string) (LogLevel, error) {
	//将字符串全部转换为小写
	content := strings.ToLower(s)
	switch content {
	case "debug":
		return DEBUG, nil
	case "info":
		return INFO, nil
	case "warning":
		return WARNING, nil
	case "error":
		return ERROR, nil
	case "fatal":
		return FATAL, nil
	default:
		err := errors.New("日志级别错误！")
		return UNKNOWN, err
	}
}
func (l *Logger) enable(loglevel LogLevel) bool {
	//loglevel 为传入的log级别
	return l.Level <= loglevel
}

//传入log错误级别
//log构造函数
func NewLog(s string, path string, name string, maxSize int) *Logger {
	level, err := parseLogLevel(s)
	if err != nil {
		panic(err)
	}
	return &Logger{Level: level, filePath: path, fileName: name, maxFileSize: maxSize}
}

//日志级别调用
func (l Logger) Debug(s string, a ...interface{}) {
	if l.enable(DEBUG) {
		funcName, fileName, line := GetInfo(2)
		log.Printf("[DEBUG]\t%v\t%v\t%v\n%s%v\t", fileName, funcName, line, s, a)
	}
}
func (l Logger) Info(s string, a ...interface{}) {
	if l.enable(INFO) {
		funcName, fileName, line := GetInfo(2)
		log.Printf("[INFO]\t%v\t%v\t%v\n%s%v\t", fileName, funcName, line, s, a)
	}
}
func (l Logger) Warning(s string, a ...interface{}) {
	if l.enable(WARNING) {
		funcName, fileName, line := GetInfo(2)
		log.Printf("[WARNING]\t%v\t%v\t%v\n%s%v\t", fileName, funcName, line, s, a)
	}
}
func (l Logger) Error(s string, a ...interface{}) {
	if l.enable(ERROR) {
		funcName, fileName, line := GetInfo(2)
		log.Printf("[ERROR]\t%v\t%v\t%v\n%s%v\t", fileName, funcName, line, s, a)
	}
}

func (l Logger) Fatal(s string, a ...interface{}) {
	if l.enable(FATAL) {
		funcName, fileName, line := GetInfo(2)
		log.Printf("[FATAL]\t%v\t%v\t%v\n%s%v\t", fileName, funcName, line, s, a)
	}
}
```

## runTimeLine.go

```go
//n表示调用函数的等级，0表示此函数,1表示调用此函数的函数，多一次等级，n多加一
func GetInfo(n int) (funcName, fileName string, line int) {
	//分别拿到调用函数的函数名、文件名、行号
	pc, file, line, ok := runtime.Caller(n)
	if !ok {
		fmt.Println("error")
	} else {
		//函数名
		funcName := runtime.FuncForPC(pc).Name()
		//文件的绝对路径
		//fmt.Println(file)
		//文件名
		fileName := path.Base(file)
		//行号
		return funcName, fileName, line
	}
	return
}
```

