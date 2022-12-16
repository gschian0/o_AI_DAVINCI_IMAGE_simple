<script>
  import { onMount } from "svelte";
  import { Configuration, OpenAIApi } from "openai";
  import mp3 from "./aqua.mp3";
  // import {predict, makeApiRequest} from 'replicate-api'
  // import Replicate from "https://cdn.jsdelivr.net/gh/nicholascelestin/replicate-js/replicate.js"
  //import {CustomButton}
  // NEVER put your token in any publically accessible client-side Javascript
  // Instead, use a proxy-- see Authentication section below

  // const bamBoom = async () => {
  //   const FullMP3 = await mp3;
  //   response = await fetch("https://45.76.9.46:5000/whisper", {
  //     FullMP3,
  //     method: "POST",
  //     headers: {
  //       "Content-Type": "application/json"
  //     }
  //   });
  //   console.log(response);
  // };

  // bamBoom();

  let outputz = [];
  let date = new Date();
  let questionString = "";
  let question = "";
  let prediction;
  let prediction2;
  let error;
  let image = "https://www.dropbox.com/s/3bitcdgjejgjaqb/zen2.jpg?raw=1";
  let g = "https://www.dropbox.com/s/3bitcdgjejgjaqb/zen2.jpg?raw=1";
  let imgVar = "https://www.dropbox.com/s/3bitcdgjejgjaqb/zen2.jpg?raw=1";
  const deepai = require("deepai"); // OR include deepai.min.js as a script tag in your HTML

  const data = {
    version: "2af375da21c5b824a84e1c459f45b69a117ec8649c2aa974112d7cf1840fc0ce",
    input: {
      text: "Dali painting of WALL·E"
    }
  };
  console.log(mp3);

  const options = {
    method: "POST",
    body: JSON.stringify(data),
    headers: {
      Authorization: `Token 18cbd34dfb33a8cfe9fd5c18fae60f9ff3207456`,
      "Content-Type": "application/json"
    }
  };

  deepai.setApiKey("d23189e7-d9d8-41b8-8967-c346d860a70d");

  const configuration = new Configuration({
    organization: "org-GxtWZIH71ZIk7DgYFhDNd3QR",
    apiKey: "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"
  });

  const openai = new OpenAIApi(configuration);
  onMount(async () => {
    const engines = await openai.listEngines();
    outputz = engines.data.data; //
  });

  async function clickImage() {
    // var resp = await deepai.callStandardApi("fantasy-world-generator", {
    //   text: `${questionString}`
    // });
    // console.log(resp);
    // console.log(resp.output_url);
    // image = resp.output_url;
    const response = await openai.createImage({
      prompt: `${questionString}`,
      n: 1,
      size: "1024x1024"
    });
    image = response.data.data[0].url;
  }

  const onSubmit = async e => {
    const response = await openai.createCompletion({
      model: "text-davinci-003",
      prompt: `Create a Dall-e 2 prompt for maximum effectiveness using the subject matter supplied to create a vivid ai generated image. 
                                                    SUBJECT: ${question}
                                                    PROMPT: `,
      max_tokens: 100,
      temperature: 0.5
    });
    //console.log(response.data.choices[0].text);
    // questionData = response.;
    questionString = response.data.choices[0].text;
  };

  const makeVariation = async () => {
    console.log("clicked");
    const resp2 = await fetch("https://api.replicate.com/v1/predictions", {
      method: "POST",
      headers: {
        Authorization: `Token 18cbd34dfb33a8cfe9fd5c18fae60f9ff3207456`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        // Pinned to a specific version of kuprel/min-dalle, fetched from:
        // https://replicate.com/kuprel/min-dalle/versions
        version:
          "2af375da21c5b824a84e1c459f45b69a117ec8649c2aa974112d7cf1840fc0ce",
        input: { text: `${questionString}`, grid_size: 1 }
      })
    });
    console.log(resp2);
    prediction2 = await resp2.json();
    console.log(prediction2);
    if (resp2.status !== 201) {
      error = prediction2.detail;
      return;
    } else if (resp2.stus !== 405) {
      error = prediction2.detail;
      return;
    }

    while (
      prediction2.status !== "succeeded" &&
      prediction2.status !== "failed"
    ) {
      await sleep(1000);
      const response = await fetch(
        "https://api.replicate.com/v1/predictions/" + prediction2.id
      );
      prediction2 = await resp2.json();
      if (resp2.status !== 200) {
        error = prediction2.detail;
        return;
      }
      console.log(prediction2);
    }
  };

  const makeAnotherImage = async () => {
    var resp = await deepai.callStandardApi("stable-diffusion", {
      text: `${questionString}`,
      grid_size: "1"
    });
    console.log(resp.output_url);
    imgVar = resp.output_url;
  };

  //download(image)
</script>
<html lang="">
<body>
<br>
 <div class="center header">
<h1 class="header">open ai?</h1>
            <img
              class="center"
              src={g}
              alt="output"
            />

        
        <br>
<p> yo gpt-3, can i kick it with davinci 003?</p>
<p> for Dall-E 2 prompt inspiration?</p>
<p>PROMPT : {console.log('What do you want a Dall-e 2 diffusion prompt regarding?') || question}</p>
</div>
<br>
<div class="center header">
   <form >
              <input
                type="text"
                name="question"
                placeholder="subject for Dall-e 2"
                bind:value={question}
              />
            <button on:click|preventDefault={onSubmit}>
              SUBMIT
            </button>
            <br><br>
            <p>WHEN PROMPT FIELD FILLS HIT SUBMIT!</p>
            <input
                type="text"
                name="prompt"
                placeholder="Press submit with prompt"
                bind:value={questionString}
              />
            <button on:click|preventDefault={clickImage}>
              SUBMIT PROMPT
            </button>
<br>

            </form>
          </div>
             <h2>{questionString}</h2>
            <br> 
            
      <div class="center header">
            <img
              class="center"
              src={image}
              alt="output"
            />
            </div>
<br>
<div class="center header">
<form>
              <button on:click|preventDefault={makeAnotherImage}>
              Make Deep-AI SD
            </button>
            <!-- <button on:click|preventDefault={makeVariation}>
              Make Variation
            </button> -->
            </form>
            </div>
            <br>
            <div class="center header">
 <img
              class="center"
              src={imgVar}
              alt="output"
            />
  </div>
        <br>
        <div class="center header">
            <h3> AI MODELS AVAILABLE AT OPEN AI </h3>
            </div>
            <br>
<div class="photos center header">
	{#each outputz as out}
	
			<p>{out.id} </p>
	
	{:else}
		<!-- this block renders when photos.length === 0 -->
		<p>loading...</p>
	{/each}
</div>

       
   
</body> 
<div><footer class="footer center">
<a class="center" href="https://www.wavefunction.dev">wavefunction</a>
<h3>zen is {date}</h3>
          </footer></div>

</html>


<style>
				@font-face {
				  font-family: "Gelasio";
				  font-style: normal;
				  font-weight: 400;
				  src: local("Gelasio Regular"), local("Gelasio-Regular"),
				    url(https://fonts.gstatic.com/s/gelasio/v1/cIf9MaFfvUQxTTqS9C6hYQ.woff2)
				      format("woff2");
				  unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+02C6, U+02DA,
				    U+02DC, U+2000-206F, U+2074, U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215,
				    U+FEFF, U+FFFD;
				}
				img {
				  border: 5px solid rgb(0, 0, 0);
				}
				body {
				  margin: 0;
				  text-align: center;
				  font-size: 34px;
				  line-height: 1.5;
				  color: #333;
				  background: rgb(105, 231, 246);
				  background: radial-gradient(
				    circle,
				    rgba(105, 231, 246, 1) 0%,
				    rgba(0, 115, 250, 1) 89%
				  );
				}
				input {
				  width: 30%;
				  height: 45;
				  text-align: center;
				}

				button {
				  height: 3.5rem;
				  font-family: Gelasio;
				  width: 8rem;
				  background-color: #aaa;
				  border-color: #00cd52;
				  color: #000000;
				  font-size: 1.25rem;
				  background-image: linear-gradient(45deg, #f1c40f 50%, transparent 50%);
				  background-position: 100%;
				  background-size: 400%;
				  transition: background 300ms ease-in-out;
				}
				button:hover {
				  background-position: 0;
				  color: #aaa;
				}
				.center {
				  display: block;
				  margin-left: auto;
				  margin-right: auto;
				  width: 70%;
				}
				body {
				  font-family: Arial, sans-serif;
				  font-size: 14px;
				  line-height: 1.5;
				  color: #333;
				  margin: 0;
				  padding: 0;
				}

				h1 {
				  font-family: Gelasio;
				  font-size: 24px;
				  font-weight: bold;
				  color: #333;
				  margin-top: 20px;
				  margin-bottom: 10px;
				}

				p {
				  margin-top: 0;
				  font-family: "Helvetica Neue", sans-serif;
				  margin-bottom: 10px;
				  bold: true;
				  font-size: 16px;
				}

				a {
				  color: #337ab7;
				  text-decoration: none;
				}

				a:hover {
				  text-decoration: underline;
				}

				.container {
				  max-width: 1200px;
				  margin: 0 auto;
				  padding: 0 15px;
				}

				.main-content {
				  padding: 20px 0;
				}

				.sidebar {
				  padding: 20px 0;
				}

				.header {
				  background-color: #f5f5f5;
				  padding: 20px;
				  text-align: center;
				  border-style: solid;
				  border-width: 10px 23px;
				}

				.footer {
				  background-color: #f5f5f5;
				  padding: 20px;
				  text-align: center;
				}
</style>
