# Blazor Virtualize end-anchor reproduction

Reproduces a blank viewport when scrolling upward after appending variable-height items and using `ScrollToItemAsync` to jump to the end of an end-anchored list.

This is a standalone .NET 11 Blazor Web App using Blazor Server. It has no package dependencies, authentication, backend data source, custom scrolling JavaScript, or third-party components. The list contains integers, with every fifth row twice as tall as the others.

## Run

Use the .NET 11 SDK. Tested with SDK **11.0.100**, ASP.NET Core runtime **11.0.0**, Windows, and Microsoft Edge.

```powershell
dotnet run --launch-profile http --urls http://localhost:54187
```

Open `http://localhost:54187/?Overscan=5` for the most reliable reproduction.

## Reproduce

1. Select **End of queue** under **Follow new entries**.
2. Drag the list's own scrollbar to approximately the middle of the list. Wait for the rows to appear.
3. Click **Add at end** nine times while remaining in the middle.
4. Click **Go to end**. Verify that the appended items appear.
5. Scroll upward within the list, continuing past the initially rendered tail. Approximately 6,000 pixels was sufficient with overscan 5 in the tested run.

**Expected:** Earlier items render as the viewport moves upward.

**Actual:** The rendered window remains at the tail. Earlier items aren't rendered, exposing the leading spacer as a large blank region. Continued scrolling can leave the viewport completely empty even though items still exist.

Reload the page before trying another variation.

## Controls

The reproduction URL uses overscan **5**, matching the original demo. Without a query parameter, the app uses **15**, matching the .NET 11 default. The blank viewport also occurred with overscan 15 in an earlier run, but not in every run; timing appears to affect the outcome.

For comparison:

- Open `http://localhost:54187/?Fixed=true` and repeat the steps. All rows are 120 pixels tall; upward scrolling worked in this control.
- With variable-height rows, repeat the sequence without adding any items. Upward scrolling worked in this control.

These are observed control results, not a claim that other combinations cannot fail.

## Relevant code

- `Components\Pages\Home.razor`: the `Virtualize<int>` component, append operation, and awaited `ScrollToItemAsync(items.Count - 1)` call.
- `wwwroot\app.css`: the scroll container and fixed/variable row heights.

Appends create a new list while preserving existing item identities. Each rendered row uses `@key`. The app uses the framework API to jump; it doesn't manipulate scroll positions itself.
