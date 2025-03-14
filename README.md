# The CJS idea

Idea spawned here after explaining I pretty much only use HTML, JS, and tailwind to someone: https://claude.ai/chat/4a21b364-74dd-47a3-94c2-aa4884d4d91f

The idea would be to create a cloudflare worker that returns HTML based on a vercel-like folder structure with params. Just like with monoflare, it'd be possible to have a worker based on a single file. We'd define the available environment in `<% type Env = {kv: KvNamespace; } %>` at the top!

This could be an augmentation of the monoflare concept. If you want html, its convenient to do things like this because you can easily open your HTML file without backend as well. It also would give options to bring back ctml: https://github.com/janwilmake/ctml. EJS also supports includes: https://github.com/mde/ejs?tab=readme-ov-file#includes

It'd be great to define workers like this, since sometimes you want regular HTML. Then, adding data would simply be a small addition. Besides, we can set up a rule that, incase you just want JSON, it can also return that under `[endpoint].json` incase it's defined in a certain way.

# Deciding not to use EJS

After trying a little I found maybe ejs is not the way to go because it creates ugly HTML if you'd render it locally as HTML. Maybe, pure HTML would be even better, given we can detect which parts are required to be rendered on the backend.

The great thing about HTML is that it can run JS inline. That means we could also choose to run the backend code at the frontend as it'd be the same style. This gives a great DX since it would allow just opening the file and run it without any server.

Fetch also works on the frontend. In HTML only mode it would simply have no auth and thats fine if the API supports that with a tiny ratelimit. In the backend, however, auth can be auto-provided by wrapping the fetch function and providing this based on the host and auth.

This is cool as it could also use fetch for kv, r2, fs, etc. It could simply use a simple server for that, that provides dummy-data that is tied to the IP if you are loading this thing as HTML. When rendered in the server, it'd be tied to a configuration provided in the `<script type="application/json"></script>` at the head.

# Conclusion

The way it's done now in [example-html](example-html.html) I need to just make a server-side parser that:

- at build time, find script env and script server (one or more) and turns this part into a js file with the credentials provided
- at run time, the js is ran and the original html replaces the server part with a promise that immediately resolves the data
- with some magic, maybe we can even run further JS server-side to actually output static HTML with all data filled in
