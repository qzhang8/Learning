# Learning

https://github.com/hi-ogawa/blog/blob/master/src/posts/2017-03-03-blink-overview--vol-3-.md

// RenderView corresponds to the content container of a renderer's subset
// of the frame tree. A frame tree that spans multiple renderers will have a
// RenderView in each renderer, containing the local frames that belong to
// that renderer. The RenderView holds non-frame-related state that is
// replicated across all renderers, and is a fairly shallow object.
// Generally, most APIs care about state related to the page content which
// should be accessed through RenderFrame instead.
//
// WARNING: Historically RenderView was the path to get to the main frame,
// and the entire frame tree, but that is no longer the case. Usually
// RenderFrame is a more appropriate surface for new code, unless the code is
// agnostic of frames and page content or structure. For more context, please
// see https://crbug.com/467770 and
// https://www.chromium.org/developers/design-documents/site-isolation.

// RenderFrame interface wraps functionality, which is specific to frames, such as
// navigation. It provides communication with a corresponding RenderFrameHost
// in the browser process.
