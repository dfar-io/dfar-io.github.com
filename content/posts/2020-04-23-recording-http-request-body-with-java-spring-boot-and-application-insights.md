---
title: Recording HTTP Request Body with Java, Spring Boot and Application Insights
author: dfar

date: 2020-04-23T14:23:23+00:00
url: /recording-http-request-body-with-java-spring-boot-and-application-insights/
categories:
  - Technology

---
Building off of my previous post [about integrating App Insights into Spring Boot][1], I also wanted to record the request body in each trace sent to Azure. This is especially useful when looking up failures, since you&#8217;ll be able to see the request body used that caused the failure.

## Important Note Regarding Privacy

Before getting started, something to consider is the issue of privacy &#8211; by activating this, you&#8217;ll be storing request body information into Azure, which can be an issue if you&#8217;re dealing with sensitive information.

If that&#8217;s the case, you should be sure to process the body extracted from this inplementation and remove the sensitive information in the payload before adding it to the request telemetry.

## Bypassing the HttpServletRequest issue

Java servlets do not allow the ability to read a response multiple times &#8211; if you try to do so by reading `getReader()` multiple times, you&#8217;ll get an IllegalStateException. To fix this, we&#8217;ll create a custom implementation of HttpServletRequest that will cache the request provided, allowing us to read the request body, and then passing this down further the Spring Boot chain.

Create the `CachedBodyHttpServletRequest` class:

<pre class="wp-block-code"><code>package com.example.demo;

import java.io.BufferedReader;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.stream.Collectors;

import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;

import org.springframework.util.StreamUtils;

public class CachedBodyHttpServletRequest extends HttpServletRequestWrapper {
    private byte&#91;] cachedBody;

    public CachedBodyHttpServletRequest(HttpServletRequest request) throws IOException {
        super(request);
        InputStream requestInputStream = request.getInputStream();
        this.cachedBody = StreamUtils.copyToByteArray(requestInputStream);
    }

    @Override
    public ServletInputStream getInputStream() throws IOException {
        return new CachedBodyServletInputStream(this.cachedBody);
    }

    @Override
    public BufferedReader getReader() throws IOException {
        // Create a reader from cachedContent
        // and return it
        ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(this.cachedBody);
        return new BufferedReader(new InputStreamReader(byteArrayInputStream));
    }

    public String getBody() throws IOException {
        return getReader().lines().collect(Collectors.joining(System.lineSeparator()));
    }
}
</code></pre>

Next, create the `CachedBodyServletInputStream` class:

<pre class="wp-block-code"><code>package com.example.demo;

import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;

import javax.servlet.ReadListener;
import javax.servlet.ServletInputStream;

public class CachedBodyServletInputStream extends ServletInputStream {
    private InputStream cachedBodyInputStream;

    public CachedBodyServletInputStream(byte&#91;] cachedBody) {
        this.cachedBodyInputStream = new ByteArrayInputStream(cachedBody);
    }

    @Override
    public boolean isFinished() {
        try {
            return cachedBodyInputStream.available() == 0;
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return false;
    }

    @Override
    public boolean isReady() {
        return true;
    }

    @Override
    public void setReadListener(ReadListener readListener) {
        throw new UnsupportedOperationException();
    }

    @Override
    public int read() throws IOException {
        return cachedBodyInputStream.read();
    }
}
</code></pre>

## Adding CachedBodyHttpServletRequest to Spring Boot Filter

To use this, you&#8217;ll create a filter that activates before processing a request, which will add the request body to the request telemetry when defined as a POST or PUT method.

Create the `CachedHttpServletRequestFilter` class:

<pre class="wp-block-code"><code>package com.example.demo;

import com.microsoft.applicationinsights.telemetry.RequestTelemetry;
import com.microsoft.applicationinsights.web.internal.RequestTelemetryContext;
import com.microsoft.applicationinsights.web.internal.ThreadContext;
import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;
import org.springframework.http.HttpMethod;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Order(value = Ordered.HIGHEST_PRECEDENCE)
@Component
@WebFilter(filterName = "ContentCachingFilter", urlPatterns = "/*")
public class CachedHttpServletRequestFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        // Skip all processing if not a POST or PUT request to improve efficiency
        if (!isPost(request) && !isPut(request)) {
            filterChain.doFilter(request, response);
            return;
        }

        RequestTelemetryContext context = ThreadContext.getRequestTelemetryContext();
        RequestTelemetry requestTelemetry = context.getHttpRequestTelemetry();

        CachedBodyHttpServletRequest cachedRequest = new CachedBodyHttpServletRequest(request);
        String body = cachedRequest.getBody();
        requestTelemetry.getProperties().put("Request Body", body);

        filterChain.doFilter(cachedRequest, response);
    }

    private boolean isPost(HttpServletRequest request) {
        return request.getMethod().equalsIgnoreCase(HttpMethod.POST.toString());
    }

    private boolean isPut(HttpServletRequest request) {
        return request.getMethod().equalsIgnoreCase(HttpMethod.PUT.toString());
    }
}
</code></pre>

## Verification

To verify, start running the application and make a few calls. When observing the requests in App Insights, you should be able to see the recorded Request Body on all POST and PUT calls:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/04/image-2-914x1024.png" alt="" class="wp-image-1163" srcset="https://40.76.37.251/wp-content/uploads/2020/04/image-2-914x1024.png 914w, https://40.76.37.251/wp-content/uploads/2020/04/image-2-268x300.png 268w, https://40.76.37.251/wp-content/uploads/2020/04/image-2-768x861.png 768w, https://40.76.37.251/wp-content/uploads/2020/04/image-2.png 1026w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure>

 [1]: https://dfar.io/integrate-application-insights-into-a-spring-boot-application/