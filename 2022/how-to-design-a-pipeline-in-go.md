[//title]: (how-to-design-a-pipeline-in-go)
[//englishtitle]: (how-to-design-a-pipeline-in-go)
[//category]: (go,concurrent-programming,design-pattern,snippet)
[//tags]: (go,concurrent-programming,design-pattern,snippet)
[//createtime]: (20221007)
[//updatetime]: (20221007)

In linux a pipe symbol '|' is used to combine two or more commands, the output of one command acts as input to another command, e.g. `ls -l | grep go` will list all files in the current directory and then filter the output to only show files that contain the string `go`.

In Go, we can use the same concept to design a pipeline:

- Which is a series of stages connected by channels.
- Each stage is a group of goroutines running the same function.
- The different stages are connected by channels, so that the output of one stage is the input of the next one.
- The output of the last stage can be collected for further processing.

![](https://cdn.liushiming.cn/img/20221007130328.png)

## Getting started

The typical way to design a pipeline in Go is to use channels.

This is a simple program:

```go
func main() {
	names := []string{"Alice","Bob","Frank"}
	for _, name := range names {
        fmt.Println("Hello ", name)
    }
}
```

Transform it to pipeline:

```go
func main() {
	names := []string{"Alice", "Bob", "Frank"}
	go printer(welcomer(producer(names)))
	fmt.Println("main finished!")
	time.Sleep(1 * time.Second)
}

func producer(names []string) chan string {
	fmt.Println("producer started!")
	ch := make(chan string)
	go func() {
		for _, name := range names {
			ch <- name
		}
		close(ch)
	}()
	fmt.Println("producer finished!")
	return ch
}

func welcomer(jobCh chan string) chan string {
	resultCh := make(chan string)
	go func() {
		for name := range jobCh {
			resultCh <- "Hello " + name
		}
		fmt.Println("welcomer finished!")
		close(resultCh)
	}()
	return resultCh
}

func printer(jobCh chan string) {
	for msg := range jobCh {
		fmt.Println(msg)
	}
	fmt.Println("printer finished!")
}
```

## Worker pool

Now let's change a bit, if a welcomer take's 1 second to welcome a person, then we have to wait for 3 seconds to finish the job.

It's a common problem in pipeline: the producer is too fast, it produces jobs faster than the consumer can consume. This will cause the channel to be full and block the producer.

What if we have three welcomers, each of them takes 1 second to welcome a person, then we can finish the job in 1 second, make sense right?

Here we go:

```go
func main() {
	names := []string{"Alice", "Bob", "Frank"}
	go printer(welcomer(producer(names)))
	fmt.Println("main finished!")
	time.Sleep(5 * time.Second)
}

func producer(names []string) chan string {
	fmt.Println("producer started!")
	ch := make(chan string)
	go func() {
		for _, name := range names {
			ch <- name
		}
		close(ch)
		fmt.Println("producer finished!")
	}()
	return ch
}

func welcomer(jobCh chan string) chan string {
	resultCh := make(chan string)
	go func() {
		var wg = sync.WaitGroup{}
		wg.Add(3)
		// start 3 workers
		for i := 0; i < 3; i++ {
			go func(id int, wg *sync.WaitGroup) {
				defer wg.Done()
				for name := range jobCh {
					time.Sleep(1 * time.Second)
					resultCh <- "Hello " + name
				}
				fmt.Printf("welcomer %d finished!\n", id)
			}(i, &wg)
		}
		// wait for all workers to finish
		wg.Wait()
		close(resultCh)
		fmt.Println("all welcomer workers finished!")
	}()
	return resultCh
}

func printer(jobCh chan string) {
	for msg := range jobCh {
		fmt.Println(msg)
	}
	fmt.Println("printer finished!")
}
```

## Job pool

Great! Now we have more workers. But from the producer -> welcomer -> printer, the job is passing by blocking channel without buffers.

We can also have a job pool to always keep some job in the pool, and the producer can produce jobs faster.

```go
func main() {
    names := []string{"Alice", "Bob", "Carol", "Dan", "Frank", "Grace", "John", "Jack", "Tom", "Jerry", "Marry", "Lily", "Lucy", "Linda"}
	go printer(welcomer(producer(names)))
	fmt.Println("main finished!")
	time.Sleep(5 * time.Second)
}

func producer(names []string) chan string {
	fmt.Println("producer started!")
	// use buffered channel to keep some jobs in the pool
	ch := make(chan string, 6)
	go func() {
		for _, name := range names {
			ch <- name
		}
		close(ch)
		fmt.Println("producer finished!")
	}()
	return ch
}

func welcomer(jobCh chan string) chan string {
	resultCh := make(chan string, 6)
	go func() {
		var wg sync.WaitGroup
		wg.Add(3)
		for i := 0; i < 3; i++ {
			go func(id int, wg *sync.WaitGroup) {
				defer wg.Done()
				for name := range jobCh {
					time.Sleep(1 * time.Second)
					resultCh <- "Hello " + name
				}
				fmt.Printf("welcomer %d finished!\n", id)
			}(i, &wg)
		}
		wg.Wait()
		close(resultCh)
		fmt.Println("all welcomer workers finished!")
	}()
	return resultCh
}

func printer(jobCh chan string) {
	for msg := range jobCh {
		fmt.Println(msg)
	}
	fmt.Println("printer finished!")
}
```

## Graceful shutdown

Everything works fine, except when we want to exit. There may still have some jobs in the pipeline, we need to wait for them to finish.

This is AKA graceful shutdown.

There are two steps:

1. Listen for OS signals, and
2. Handle those signals:
   - Producer: stop producing new job.
   - Welcomer & printer: finish current jobs, including those in the buffer.
   - Main: wait for all workers to finish.

```go
func main() {
	names := []string{"Alice", "Bob", "Carol", "Dan", "Frank", "Grace", "John", "Jack", "Tom", "Jerry", "Marry", "Lily", "Lucy", "Linda"}
	term := make(chan os.Signal, 1)
	cancel := make(chan struct{}, 1)
	completed := make(chan struct{}, 1)
	// listen for OS signals, and send to term channel
	signal.Notify(term, syscall.SIGINT, syscall.SIGTERM)
	go printer(completed, welcomer(producer(names, cancel)))
	// block until signal received
	<-term
	fmt.Println("*********************************shutdown signal received*********************************")
	cancel <- struct{}{}
	<-completed
	fmt.Println("all on going job finished, main exit!")
}

func producer(names []string, done chan struct{}) chan string {
	fmt.Println("producer started!")
	ch := make(chan string, 6)
	go func() {
		defer close(ch)
	loop:
		for _, name := range names {
			select {
			case <-done:
				fmt.Println("cancel jobs!")
				break loop
			default:
				ch <- name
			}
		}
		fmt.Println("producer finished!")
	}()
	return ch
}

func welcomer(jobCh chan string) chan string {
	resultCh := make(chan string, 6)
	go func() {
		defer close(resultCh)
		var wg sync.WaitGroup
		wg.Add(3)
		for i := 0; i < 3; i++ {
			go func(id int, wg *sync.WaitGroup) {
				defer wg.Done()
				for name := range jobCh {
					time.Sleep(time.Millisecond * time.Duration(1000+rand.Intn(2000)))
					resultCh <- "Hello " + name
				}
				fmt.Printf("welcomer %d finished!\n", id)
			}(i, &wg)
		}
		wg.Wait()
		fmt.Println("all welcomer workers finished!")
	}()
	return resultCh
}

func printer(completed chan struct{}, jobCh chan string) {
	for msg := range jobCh {
		fmt.Println(msg)
	}
	completed <- struct{}{}
	fmt.Println("printer finished!")
}
```

output:

```text
producer started!
Hello Alice
^C*********************************shutdown signal received*********************************
cancel jobs!
producer finished!
Hello Dan
Hello Carol
Hello Bob
Hello Frank
Hello John
Hello Jack
Hello Grace
welcomer 0 finished!
Hello Tom
welcomer 1 finished!
Hello Marry
welcomer 2 finished!
all welcomer workers finished!
Hello Jerry
printer finished!
all on going job finished, main exit!
```

## Abstraction

We can abstract the pipeline into something like linked list:

```text
Producer -> [NodeA -> NodeB -> NodeC -> ... ] ---> Consumer
```

The Producer is not included in the Pipeline, it is the input of the Pipeline.

The consumer:

- Could be the last node in the pipeline (when last node doesn't have any output),
- Or outside the pipeline (a function to handle the output of the pipeline).

It's a pull model rather than push model. The consumer pulls the data from the pipeline, and each node pulls data from the previous node. So actually we start the consumer first, and then start the pipeline (right to left).

### Node abstraction

First we define the interface of the node:

```go
type INode interface {
	SetNext(node INode)
	JobReceiver(job interface{})
	Start(ctx context.Context)
	wait()
}
```

Then we define the node struct:

```go
type Node[T any] struct {
	// name identifies the node, used for debugging
	name           string
	// workFunc defines how to do the job
	workFunc       func(T) (any, error)
	// ingestChan is the channel to receive job from previous node
	ingestChan     chan T
	// jobChan is the channel to store the job, it's a buffered channel
	jobChan        chan T
    // workerPool is the pool of workers to do the job
	workerPoolSize int
	// resultChan is the channel to send the result to outside of pipeline, last node could have this channel
	resultChan     chan any
    // wg is used to wait the node process completed
	wg             *sync.WaitGroup
	// next is the next node in the pipeline
	next           INode
}
```

Full code:

```go
type INode interface {
	SetNext(node INode)
	JobReceiver(job interface{})
	Start(ctx context.Context)
	wait()
}

// Node defines a node in a pipeline.
// name is the node identifier.
// workFunc is the function actually do the work.
// ingestChan is the channel to receive job from previous node.
// jobChan is the channel to receive job from ingestChan, it's a buffered channel.
// workerPoolSize is the size of the worker pool.
// resultChan is the channel to send the result to. (only maybe used in the last node)
// next is the next node in the pipeline.
type Node[T any] struct {
	name           string
	workFunc       func(T) (any, error)
	ingestChan     chan T
	jobChan        chan T
	workerPoolSize int
	resultChan     chan any
	wg             *sync.WaitGroup
	next           INode
}

var _ INode = (*Node[int])(nil)

func NewNode[T any](name string, workFunc func(T) (any, error), jobBuffer int, workerPoolSize int, resultChan ...chan any) *Node[T] {
	var node = &Node[T]{
		name:           name,
		workFunc:       workFunc,
		ingestChan:     make(chan T),
		jobChan:        make(chan T, jobBuffer),
		workerPoolSize: workerPoolSize,
		wg:             &sync.WaitGroup{},
	}
	if len(resultChan) > 0 {
		node.resultChan = resultChan[0]
	}
	return node
}

// SetNext set the next node in the pipeline
func (n *Node[T]) SetNext(node INode) {
	n.next = node
}

// JobReceiver receive job from previous node
func (n *Node[T]) JobReceiver(job interface{}) {
	switch t := job.(type) {
	case T:
		n.ingestChan <- job.(T)
	default:
		panic(fmt.Sprintf("job type %T is not accept", t))
	}
}

// Start the workers
func (n *Node[T]) Start(ctx context.Context) {
	// listen the job from previous node
	go func() {
		for {
			select {
			case job := <-n.ingestChan:
				n.jobChan <- job
			case <-ctx.Done():
				logrus.Tracef("%s received cancellation signal, closing jobChan!\n", n.name)
				close(n.jobChan)
				logrus.Tracef("%s closed jobChan\n", n.name)
				return
			}
		}
	}()
	// start [workerPoolSize] of worker to do the job
	n.wg.Add(n.workerPoolSize)
	for i := 0; i < n.workerPoolSize; i++ {
		go n.work(n.wg, i)
	}
}

func (n *Node[T]) wait() {
	n.wg.Wait()
}

func (n *Node[T]) work(wg *sync.WaitGroup, id int) {
	logrus.Tracef("%s worker %d starting\n", n.name, id)
	defer wg.Done()
	for job := range n.jobChan {
		// working on the job
		result, err := n.workFunc(job)
		if err != nil {
			logrus.Error("do work failed: ", err)
			continue
		}
		logrus.Tracef("%s worker %v finished work\n", n.name, id)
		// pipeline to next node
		if n.next != nil {
			logrus.Tracef("%s worker %v passed to next node\n", n.name, id)
			n.next.JobReceiver(result)
		}
		// send result to resultChan (usually for the last node)
		if result != nil && n.resultChan != nil {
			n.resultChan <- result
		}
	}
	logrus.Tracef("%s worker %d interrupted\n", n.name, id)
}
```

### Pipeline abstraction

Pipeline is a linked list of nodes,

Full code:

```go
type Pipeline struct {
	nodes []INode
}

func New(nodes ...INode) *Pipeline {
	if len(nodes) == 0 {
		return nil
	}
	if len(nodes) == 1 {
		return &Pipeline{nodes: nodes}
	} else {
		for i := 0; i < len(nodes)-1; i++ {
			nodes[i].SetNext(nodes[i+1])
		}
	}
	return &Pipeline{nodes: nodes}
}

func (p *Pipeline) Start(ctx context.Context, cleanup ...func()) func() {
	if p == nil {
		return nil
	}
	cancelFuncs := make([]context.CancelFunc, len(p.nodes))
	for i := 0; i < len(p.nodes); i++ {
		ctx, cancelFunc := context.WithCancel(ctx)
		go p.nodes[i].Start(ctx)
		cancelFuncs[i] = cancelFunc
	}

	// cancel pipeline function, cancel nodes from upstream to downstream
	once := sync.Once{}
	cancel := func() {
		once.Do(func() {
			for i, _ := range cancelFuncs {
				cancelFuncs[i]()
				p.nodes[i].wait()
			}
			logrus.Info("All workers finished")
		})
	}

	// handle sigterm, graceful shutdown
	go func() {
		termChan := make(chan os.Signal)
		signal.Notify(termChan, syscall.SIGINT, syscall.SIGTERM, syscall.SIGKILL)
		// blocks here until interrupted
		<-termChan
		logrus.Info("*********************************Shutdown signal received*********************************")
		cancel()
		if len(cleanup) > 0 {
			for _, f := range cleanup {
				f()
			}
		}
	}()
	return cancel
}

func (p *Pipeline) JobReceiver(job interface{}) {
	if len(p.nodes) == 0 {
		return
	}
	p.nodes[0].JobReceiver(job)
}
```

## Refactor the example

```go
func main() {
	names := []string{"Alice", "Bob", "Carol", "Dan", "Frank", "Grace", "John", "Jack", "Tom", "Jerry", "Marry", "Lily", "Lucy", "Linda"}
	node0 := pipeline.NewNode[string]("welcomer", welcomer, 6, 3)
	node1 := pipeline.NewNode[string]("printer", printer, 6, 3)
	p := pipeline.New(node0, node1)
	cancel := p.Start()
	producer(names, p)
	cancel()
}

func producer(names []string, pipeline *pipeline.Pipeline) {
	for _, name := range names {
		pipeline.JobReceiver(name)
	}
}

func welcomer(name string) (any, error) {
	time.Sleep(time.Millisecond * time.Duration(1000+rand.Intn(2000)))
	return "Hello " + name, nil
}

func printer(msg string) (any, error) {
	fmt.Println(msg)
	return nil, nil
}
```

## Performance measurement

TBD:

- How to measure the performance of pipeline?
- How to prevent pipeline from blocking/starvation?
