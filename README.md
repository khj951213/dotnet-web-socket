# .NET Core Web Socket

### Code

```c#
app.UseWebSockets();
app.Use(async (context, next) =>
{
    if (context.Request.Path == "/ws")
    {
        if (context.WebSockets.IsWebSocketRequest)
        {
            using var ws = await context.WebSockets.AcceptWebSocketAsync();
            while (true)
            {
                var message = "{\"time:\": " + "\"" + DateTime.Now.ToString("HH:mm:ss") + "\"}";
                var bytes = Encoding.UTF8.GetBytes(message);
                var arraySegment = new ArraySegment<byte>(bytes, 0, bytes.Length);
                if (ws.State == WebSocketState.Open)
                {
                    await ws.SendAsync(arraySegment,
                        WebSocketMessageType.Text,
                        true,
                        CancellationToken.None);
                }
                else if (ws.State == WebSocketState.Closed || ws.State == WebSocketState.Aborted)
                {
                    break;
                }
                Thread.Sleep(1000);
            }
        }
        else
        {
            context.Response.StatusCode = StatusCodes.Status400BadRequest;
        }
    }
    else
    {
        await next(context);
    }
});
```
