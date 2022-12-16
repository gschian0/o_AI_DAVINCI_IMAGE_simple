<script>
const data = {
  version: "2af375da21c5b824a84e1c459f45b69a117ec8649c2aa974112d7cf1840fc0ce",
  input: {
    text: "Dali painting of WALL·E"
  }
};

const options = {
  method: "POST",
  body: JSON.stringify(data),
  headers: {
    Authorization: `Token 18cbd34dfb33a8cfe9fd5c18fae60f9ff3207456`,
    "Content-Type": "application/json"
  }
};

onClick() 
{
fetch("https://api.replicate.com/v1/predictions", options)
  .then(response => response.json())
  .then(data => {
    console.log(data)
    // do something with the response data
  });
}
  </script>

  <div>
  <button>boom</button>
  </div>


  <style>
  </style>