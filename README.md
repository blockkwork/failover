# failover
go library that allows you to add multiple urls and send a request to the active one


## 💡 Info
**How it works?** failover saves your url and checks for availability every n seconds. if the url is unavailable, it saves and checks to wait for availability.

**Request function:** Request uses round robin algorithm to select the url

## 🚀 Usage
```go

func main() {
	f := failover.New(checkConn,
		failover.OptCheckUrlDelay(30),
		failover.OptMaxAttempts(10),
		failover.OptCheckUrlBeforeAdding(true),
		failover.OptReqOnErr(failover.ReqOnErrRemoveAndReconnect),
	)

	// first way to add url
	f.AddUrl(f.MustParseURL("https://google.com"))
	f.AddUrl(f.MustParseURL("https://fjxiofujosidfujs.com"))
	f.AddUrl(f.MustParseURL("https://fbi.gov"))

	// second way to add url
	f.AddUrls(f.MustParseURL("https://youtube.com"), f.MustParseURL("https://spotify.com"), f.MustParseURL("https://github.com"))

	fmt.Println(f.Request(requestFunc))
	fmt.Println(f.Request(requestFunc, failover.OptReqOnErr(failover.ReqOnErrIgnore))) // local option (only in this function) to ignore error
}

func checkConn(url *url.URL) error {
	resp, err := http.Get(url.String())
	if err != nil {
		return err
	}
	if resp.StatusCode != http.StatusOK {
		return fmt.Errorf("unexpected status code: %d", resp.StatusCode)
	}
	return nil
}

func requestFunc(url *url.URL) error {
	fmt.Println("Request:", url.String())
	_, err := http.Get(url.String())
	if err != nil {
		return err
	}
	return nil
}

```
