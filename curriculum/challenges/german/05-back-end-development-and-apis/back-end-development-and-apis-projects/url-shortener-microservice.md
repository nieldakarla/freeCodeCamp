---
id: bd7158d8c443edefaeb5bd0e
title: URL-Verkürzungs-Microservice
challengeType: 4
forumTopicId: 301509
dashedName: url-shortener-microservice
---

# --description--

Erstelle eine vollständige JavaScript-Anwendung, die eine ähnliche Funktionalität wie <a href="https://url-shortener-microservice.freecodecamp.rocks" target="_blank" rel="noopener noreferrer nofollow">https://url-shortener-microservice.freecodecamp.rocks</a> aufweist. Bei der Arbeit an diesem Projekt musst du deinen Code mit einer der folgenden Methoden schreiben:

-   Klone <a href="https://github.com/freeCodeCamp/boilerplate-project-urlshortener/" target="_blank" rel="noopener noreferrer nofollow">diese GitHub-Repo</a> und stelle dein Projekt lokal fertig.
-   Use <a href="https://gitpod.io/?autostart=true#https://github.com/freeCodeCamp/boilerplate-project-urlshortener/" target="_blank" rel="noopener noreferrer nofollow">our Gitpod starter project</a> to complete your project.
-   Verwende einen Site-Builder deiner Wahl, um das Projekt abzuschließen. Achte darauf, alle Dateien von unserem GitHub-Repo zu integrieren.

# --instructions--

**HINWEIS:** Vergiss nicht, eine Body-Parsing-Middleware zu verwenden, um POST-Anfragen zu berarbeiten. Du kannst außerdem die Funktion `dns.lookup(host, cb)` aus dem `dns`-Kernmodul verwenden, um eine übermittelte URL zu verifizieren.

# --hints--

Du solltest dein eigenes Projekt bereitstellen, nicht die Beispiel-URL.

```js
(getUserInput) => {
  assert(
    !/.*\/url-shortener-microservice\.freecodecamp\.rocks/.test(
      getUserInput('url')
    )
  );
};
```

Du kannst eine URL an `/api/shorturl` POSTEN und eine JSON-Antwort mit `original_url`- und `short_url`-Eigenschaften erhalten. Hier siehst du ein Beispiel: `{ original_url : 'https://freeCodeCamp.org', short_url : 1}`

```js
async (getUserInput) => {
  const url = getUserInput('url');
  const urlVariable = Date.now();
  const fullUrl = `${url}/?v=${urlVariable}`
  const res = await fetch(url + '/api/shorturl', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: `url=${fullUrl}`
  });
  if (res.ok) {
    const { short_url, original_url } = await res.json();
    assert.isNotNull(short_url);
    assert.strictEqual(original_url, `${url}/?v=${urlVariable}`);
  } else {
    throw new Error(`${res.status} ${res.statusText}`);
  }
};
```

Wenn du `/api/shorturl/<short_url>` besuchst, wirst du zur ursprünglichen URL weitergeleitet.

```js
async (getUserInput) => {
  const url = getUserInput('url');
  const urlVariable = Date.now();
  const fullUrl = `${url}/?v=${urlVariable}`
  let shortenedUrlVariable;
  const postResponse = await fetch(url + '/api/shorturl', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: `url=${fullUrl}`
  });
  if (postResponse.ok) {
    const { short_url } = await postResponse.json();
    shortenedUrlVariable = short_url;
  } else {
    throw new Error(`${postResponse.status} ${postResponse.statusText}`);
  }
  const getResponse = await fetch(
    url + '/api/shorturl/' + shortenedUrlVariable
  );
  if (getResponse) {
    const { redirected, url } = getResponse;
    assert.isTrue(redirected);
    assert.strictEqual(url,fullUrl);
  } else {
    throw new Error(`${getResponse.status} ${getResponse.statusText}`);
  }
};
```

Wenn du eine ungültige URL übermittelst, die nicht dem gültigen `http://www.example.com`-Format folgt, wird die JSON-Antwort `{ error: 'invalid url' }` enthalten

```js
async (getUserInput) => {
  const url = getUserInput('url');
  const res = await fetch(url + '/api/shorturl', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: `url=ftp:/john-doe.invalidTLD`
  });
  if (res.ok) {
    const { error } = await res.json();
    assert.isNotNull(error);
    assert.strictEqual(error.toLowerCase(), 'invalid url');
  } else {
    throw new Error(`${res.status} ${res.statusText}`);
  }
};
```

