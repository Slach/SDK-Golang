# Recast.AI - SDK-Golang

[logo]: https://github.com/RecastAI/SDK-NodeJs/blob/master/misc/logo-inline.png "Recast.AI"

![Recast.AI Logo][logo]

Recast.AI official SDK in Golang.
Package documentation: https://godoc.org/github.com/RecastAI/SDK-Golang/recast

## Synospis

This module is a Golang interface to the [Recast.AI](https://recast.ai) API. It allows you to make request to your bots

## Installation


```bash
go get -t github.com/RecastAI/SDK-golang/recast
```

## Usage

### Package

```go
import "github.com/RecastAI/SDK-Golang/recast"

func main() {
	client := recast.NewClient(YOUR_TOKEN, "en")

    response, err := client.TextRequest(YOUR_TEXT, nil)
    if err != nil {
            // Handle error
    }

    intent, err := response.Intent()
    if err != nil {
		// No intent found
    } else if intent.Slug == YOUR_INTENT {
        // Do your code...
    }
}
```

## Specs

### Structs

This module contains 4 main structures, as follows:

* recast.Client is the client allowing you to make requests.
* recast.Response contains the response from [Recast.AI](https://recast.ai).
* recast.Entity represents an entity found by Recast.AI in your user's input.
* recast.Intent represents an intent of the response

Don't hesitate to dive into the code, it's commented ;)

### Client

The Recast.AI Client can be instanciated with a token and provides the two following methods:

* textRequest(text string, opts ReqOpts) *Performs a text request*
* fileRequest(file string, opts ReqOpts) *Performs a voice file request*

*Accepted options are "Token" to override the token of the client, and "Language", to specify the language of the input*

If no language is provided in the request, Recast.AI does the following:

* TextRequest: the language of the text is detected and is used for processing if your bot has expressions for it, else your bot's primary language is used for processing.
* VoiceRequest: your bot's primary language is used for processing as we do not provide language detection for speech.


```go
import "github.com/RecastAI/SDK-Golang/recast"

func main() {
    var client *recast.Client
    var response recast.Response

    client = &recast.Client{}

    response, err := client.FileRequest(YOUR_VOICE_FILE, &recast.ReqOpts{
		Token: YOUR_TOKEN,
		Language: "en",
	})
    if err != nil {
		// Handle error
    }
}
```

### Response
The Response struct contains the following attributes:
```go
type Response struct {
	UUID      string
	Source    string
	Intents   []Intent
	Act       string
	Type      string
	Sentiment string
	Entities  map[string][]Entity
	Language  string
	Version   string
	Timestamp time.Time
	Status    int
}
```

The Recast.AI Response is generated after a call with the two previous methods and contains the following methods:

* Intent() *Intent returns the first matched intent, or an error if no intent where matched*
* IsAbbreviation() *IsAbbreviation returns whether or not the sentence is asking for an abbreviation*
* IsEntity() *IsEntity returns whether or not the sentence is asking for an entity*
* IsDescription() *IsDescription returns whether or not the sentence is asking for an description*
* IsHuman() *IsHuman returns whether or not the sentence is asking for an human*
* IsLocation() *IsLocation returns whether or not the sentence is asking for an location*
* IsNumber() *IsNumber returns whether or not the sentence is asking for an number*
* IsPositive() *IsPositive returns whether or not the sentiment is positive*
* IsVeryPositive() *IsVeryPositive returns whether or not the sentiment is very positive*
* IsNeutral() *IsNeutral returns whether or not the sentiment is neutral*
* IsNegative() *IsNegative returns whether or not the sentiment is negative*
* IsVeryNegative() *IsVeryNegative returns whether or not the sentiment is very negative*
* IsAssert() *IsAssert returns whether or not the sentence is an assertion*
* IsCommand() *IsCommand returns whether or not the sentence is a command*
* IsWhQuery() *IsWhQuery returns whether or not the sentence is a wh query*
* IsYnQuery() *IsYnQuery returns whether or not the sentence is a yes-no question*
* Get(name string) *Returns the first entity matching -name-*
* All(name string) *Returns all entities matching -name-*

```go
response, err := client.TextRequest("Give me a recipe with asparagus. And a recipe with tomatoes.")
if err != nil {
// Handle error
}

intent, err := response.Intent()
if err != nil {
// No intent matches input
}

if intent.Slug == "recipe" {
    var ingredients []recast.Entity

    // get all 'ingredients' entities
    ingredients = response.All("ingredients")
}
```

### Entity
The Response struct contains the following attributes:
```go
type Entity struct {
	Data       map[string]interface{}
	Name       string
	Confidence float64
}
```

The Recast.AI Entity is generated by the Recast.AI Sentence and provides the following method:

* Get(field string) *Returns an interface{} corresponding to a field of the entity*

Attributes can be accessed by the Get method which can be one of the following:

* hex
* value
* deg
* formatted
* lng
* lat
* unit
* code
* person
* number
* gender
* next
* grain
* order

```go
response, _ := client.TextRequest("What's the weather in San Francisco?")
var location recast.Entity
location = response.Get("location")
intent, _ := response.Intent()
if intent.Slug == "weather" && location != nil {
	fmt.Printf("You asked me for the weather in %s\n", location.Get("formated").(string))
}
```
***Recast.AI entity fields type are dependant on the entity itself so Get returns an interface{}, you must do a type assertion as follows:***
* Numbers: float64
* Strings: string

Refer to [Recast.Ai Entities Manual](https://man.recast.ai/#list-of-entities) for details about entities



## More

You can view the whole API reference at [man.recast.ai](https://man.recast.ai).

You can follow us on Twitter at [@recastai](https://twitter.com/recastai) for updates and releases.



## Contributors

[François Triquet](https://github.com/ftriquet), francois.triquet@recast.ai

[Saif Abid](https://github.com/saifabid)

## License

Copyright (c) [2016] [Recast.AI](https://recast.ai)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
