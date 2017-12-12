FROM alpine:3.6

RUN apk add --no-cache fortune
RUN fortune -s > /fortune.txt

CMD ["cat", "/fortune.txt"]