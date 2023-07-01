[油猴脚本-goodreads](https://github.com/cloudswave/blog/issues/28)

```
// ==UserScript==
// @name         豆瓣读书goodreads
// @namespace    https://github.com/cloudswave/
// @version      0.1.0
// @description  从goodreads摘取评分显示在豆瓣读书条目页面
// @author       ethanzhu
// @match        https://book.douban.com/subject/*
// @connect      www.goodreads.com
// @grant        GM_xmlhttpRequest
// @license      MIT
// ==/UserScript==

function buildCommentItem(props) {
    return `
<li class="comment-item" data-cid="549875751">
    <div class="comment">
        <h3>
            <span class="comment-info">
                <a href=${props.url}>${props.author}</a>
                <span class="user-stars allstar${props.rating}0 rating" title="推荐"></span>
                <span>${props.date}</span>
            </span>
        </h3>
        <p class="comment-content">
            <span class="short">${props.content}</span>
        </p>
    </div>
</li>
`
}

function buildRatingInfo(props, bookId) {
    const starValue = Math.round(props.rating) * 5
    const url = `https://www.goodreads.com/book/show/${bookId}`
    return `
    <div class="rating_wrap clearbox" rel="v:rating">
        <div class="rating_logo">goodreads评分</div>
        <div class="rating_self clearfix" typeof="v:Rating">
            <strong class="ll rating_num " property="v:average"> ${props.rating} </strong>
            <span property="v:best" content="10.0"></span>
            <div class="rating_right ">
                <div class="ll bigstar${starValue}"></div>
                <div class="rating_sum">
                    <span class="">
                        <a href="${url}" class="rating_people"><span property="v:votes">${props.count}</span>人评价</a>
                    </span>
                </div>
            </div>
        </div>
    </div>
    `
}

function getISBN() {
    const metaInfo = document.querySelector('head > script[type="application/ld+json"]')
    if (metaInfo) {
        return JSON.parse(metaInfo.textContent)['isbn']
    }
}

function isEnglishBook(isbn) {
    return (typeof isbn === 'string' && isbn[3] === '0')
}

function Request(url, method, opt = {}) {
    Object.assign(opt, {
        url,
        timeout: 5000,
        method: method
    })

    return new Promise((resolve, reject) => {
        opt.onerror = opt.ontimeout = reject
        opt.onload = resolve
        GM_xmlhttpRequest(opt)
    })
}


function parseBooklogRating(doc) {
    const ratingDiv = doc.querySelector('div[class="rating-value"]')
    const rating = ((+ratingDiv.innerText.split('\n')[0])*2).toFixed(1)
    const count = +doc.querySelector('span[itemprop="reviewCount"]').innerText
    return {
        rating,
        count
    }
}

function ISBN13ToISBN10(isbn13) {
    const commonChars = isbn13.slice(3, -1)
    let sum = 0
    for (const [idx, char] of Array.from(commonChars).entries()) {
        sum += (10 - idx) * parseInt(char)
    }
    let checkDigest = (11 - (sum % 11)) % 11
    checkDigest = checkDigest !== 10 ? checkDigest.toString() : 'X'
    return commonChars + checkDigest
}

(function () {
    'use strict'
    const isbn = getISBN()
    if (!(isbn && isEnglishBook(isbn))) {
        // return
    }
    console.log(isbn);
    const navTab = document.querySelector('div[class="nav-tab"]')
    navTab.firstElementChild.insertAdjacentHTML('beforeend', `<span>/<span><a class="short-comment-tabs" href="booklog" data-tab="booklog">ブクログ</a>`)
    const commentListWrapper = document.getElementById('comment-list-wrapper')
    commentListWrapper.insertAdjacentHTML('beforeend', `<div id="comments" class="comment-list booklog noshow"><ul id="booklog-review-list"></ul></div>`)
    const booklogReviewList = document.getElementById('booklog-review-list')
    const RatingInfoList = document.getElementById('interest_sectl')
    const isbn10 = ISBN13ToISBN10(isbn)


    Request(`https://www.goodreads.com/book/auto_complete?format=json&q=${isbn}`, 'GET').then(res => {
        // console.log(res);
        const response = JSON.parse(res.response);
        if(response.length === 0) {
            console.log(`goodreads ${isbn} not found!!!`);
            RatingInfoList.insertAdjacentHTML('beforeend', '<span class="">goodreads暂无记录</span>')
            return Promise.reject(0);
        }
        const bookId = response[0].bookId;
        console.log(`goodreads found bookId ${bookId}`);
        RatingInfoList.insertAdjacentHTML('beforeend', buildRatingInfo({rating: response[0].avgRating*2, count: response[0].ratingsCount}, bookId))
        return bookId;
    }).then(bookId => {
/*         return Request(`https://www.goodreads.com/book/show/${bookId}`, 'GET').then(res => {
            console.log(res);
             const responseText = res.responseText;
             return new DOMParser().parseFromString(responseText, "text/html")
        }); */
    }).then(doc => {
        // console.log(doc);
        // RatingInfoList.insertAdjacentHTML('beforeend', buildRatingInfo(parseBooklogRating(doc)))
    })
})();
```

![image](https://user-images.githubusercontent.com/5915548/218773802-a5bc35aa-7824-46a1-b9be-61db7c4ad511.png)


