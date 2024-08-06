# Malloc lab 

> 隐式列表  78 / 100 points

``` c 
/*
 * mm-naive.c - The fastest, least memory-efficient malloc package.

 */
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>
#include <unistd.h>
#include <string.h>

#include "mm.h"
#include "memlib.h"

team_t team = {
    /* Team name */
    "Cranberry", // 蔓越莓
    /* First member's full name */
    "Lizi",
    /* First member's email address */
    "liansekong@gmail.com",
    /* Second member's full name (leave blank if none) */
    "",
    /* Second member's email address (leave blank if none) */
    ""};

/* single word (4) or double word (8) alignment */
#define ALIGNMENT 8

/* rounds up to the nearest multiple of ALIGNMENT */
#define ALIGN(size) (((size) + (ALIGNMENT - 1)) & ~0x7)

#define SIZE_T_SIZE (ALIGN(sizeof(size_t)))

#define WSIZE 4
#define DSIZE 8

// bytes
#define CHUNKSIZE (1 << 12) // 4096 bytes ->  4kb page size

#define MAX(x, y) ((x) > (y) ? (x) : (y))

// Pack a size and allocated bit into a word
#define PACK(size, alloc) ((size) | (alloc))

// Read and write a word at address p
#define GET(p) (*(unsigned int *)(p))
#define PUT(p, val) (*(unsigned int *)(p) = (val))

// Read the size and allocated fields from address p
// Size 为整个块大小
#define GET_SIZE(p) (GET(p) & ~0x7)
#define GET_ALLOC(p) (GET(p) & 0x1)

// bp指向第一个有效载荷字节
#define HDRP(bp) ((char *)(bp) - WSIZE)
#define FTRP(bp) ((char *)(bp) + GET_SIZE(HDRP(bp)) - DSIZE)

#define NEXT_BLKP(bp) ((char *)(bp) + GET_SIZE(((char *)(bp) - WSIZE)))
#define PREV_BLKP(bp) ((char *)(bp) - GET_SIZE(((char *)(bp) - DSIZE)))

static void *extend_heap(size_t words);
static void *coalesce(void *bp);
static void *find_fit(size_t asize);
static void place(void *bp, size_t size);

void *mm_malloc(size_t size);

char *heap_listp;

/*
 * mm_init - initialize the malloc package.
 */
int mm_init(void)
{

    if ((heap_listp = mem_sbrk(4 * WSIZE)) == (void *)-1)
    {
        return -1;
    }

    PUT(heap_listp, 0);                            // 起始位置
    PUT(heap_listp + (1 * WSIZE), PACK(DSIZE, 1)); // 序言块 8/1
    PUT(heap_listp + (2 * WSIZE), PACK(DSIZE, 1)); // 序言块 8/1
    PUT(heap_listp + (3 * WSIZE), PACK(0, 1));     // 结尾块 0/1

    // 指向第二个序言块
    heap_listp += (2 * WSIZE);

    if (extend_heap(CHUNKSIZE) == NULL)
        return -1;
    // printf("init finsh!\n");
    return 0;
}

/**
 * 首次匹配
 */

static void *find_fit(size_t asize)
{
    char *start_bp = heap_listp + DSIZE;
    while (GET_SIZE(HDRP(start_bp)) > 0)
    {
        if (GET_ALLOC(HDRP(start_bp)) == 0 && (GET_SIZE(HDRP(start_bp)) >= asize))
        {
            return start_bp;
        }
        start_bp = NEXT_BLKP(start_bp);
    }
    return NULL;
}

/**
 * 设置分配块
 * 1. 设置分配位为1
 * 2. 分割空闲块
 */
static void place(void *bp, size_t size)
{
    size_t cur_bk_size = GET_SIZE(HDRP(bp));
    if (cur_bk_size - size >= DSIZE * 2)
    {
        PUT(HDRP(bp), PACK(size, 1));
        PUT(FTRP(bp), PACK(size, 1));
        PUT(HDRP(NEXT_BLKP(bp)), PACK(cur_bk_size - size, 0));
        PUT(FTRP(NEXT_BLKP(bp)), PACK(cur_bk_size - size, 0));
    }
    else
    {
        PUT(HDRP(bp), PACK(cur_bk_size, 1));
        PUT(FTRP(bp), PACK(cur_bk_size, 1));
    }
}

/*
 * mm_malloc - Allocate a block by incrementing the brk pointer.
 *     Always allocate a block whose size is a multiple of the alignment.
 */
void *mm_malloc(size_t size)
{
    if (size == 0)
    {
        return NULL;
    }
    // payload + padding + hdr + ftr
    // Double word algin
    size_t asize = ALIGN(size) + DSIZE;
    size_t extend_size;
    char *bp;
    // 寻找空闲块
    if ((bp = find_fit(asize)) != NULL)
    {
        place(bp, asize);
        return bp;
    }
    // 无空闲列表，则向系统申请分配内存， 最小单位为 4kb
    extend_size = MAX(asize, CHUNKSIZE);
    if ((bp = extend_heap(extend_size)) == NULL)
        return NULL;
    place(bp, asize);
    return bp;
}

/*
 * mm_free - Freeing a block does nothing.
 */
void mm_free(void *bp)
{
    // 获取当前块的大小
    size_t size = GET_SIZE(HDRP(bp));
    // 设置头尾为未分配
    PUT(HDRP(bp), PACK(size, 0));
    PUT(FTRP(bp), PACK(size, 0));
    // 合并
    coalesce(bp);
}

/*
 * mm_realloc - Implemented simply in terms of mm_malloc and mm_free
 */

void *mm_realloc(void *ptr, size_t size)
{
    if (ptr == NULL)
    {
        return mm_malloc(size);
    }
    if (size == 0)
    {
        mm_free(ptr);
        return NULL;
    }

    size_t asize = ALIGN(size) + DSIZE;
    size_t cur_bk_size = GET_SIZE(HDRP(ptr));

    if (cur_bk_size == asize)
    {
        return ptr;
    }
    else if (cur_bk_size < asize)
    {
        // expand
        char *new_ptr = mm_malloc(size);
        memcpy(new_ptr, ptr, cur_bk_size - DSIZE);
        mm_free(ptr);
        return new_ptr;
    }
    else
    {
        // shrink
        if (cur_bk_size - asize <= 2 * DSIZE)
        {
            return ptr;
        }
        else
        {
            place(ptr, asize);
            return ptr;
        }
    }
}

// 1. 堆初始化时调用
// 2. mm_malloc不能找到一个合适的匹配块时
static void *extend_heap(size_t size)
{
    size_t asize = size % CHUNKSIZE == 0 ? size : ((size / CHUNKSIZE) + 1) * CHUNKSIZE;
    char *bp;
    if ((bp = mem_sbrk(asize)) == (void *)-1)
    {
        return NULL;
    }
    // 分配block时bp指向块的头部表， bp前一个块为之前的结尾块（0/1）
    // 此时将之前的结尾块当作头部，现在分配块的最后一个word设为结尾块
    PUT(HDRP(bp), PACK(asize, 0));
    PUT(FTRP(bp), PACK(asize, 0));
    PUT(HDRP(NEXT_BLKP(bp)), PACK(0, 1));
    return coalesce(bp);
}

static void *coalesce(void *bp)
{
    size_t prev_alloc = GET_ALLOC(FTRP(PREV_BLKP(bp)));
    size_t next_alloc = GET_ALLOC(FTRP(NEXT_BLKP(bp)));

    size_t size = GET_SIZE(HDRP(bp));
    if (prev_alloc && next_alloc)
    {
        return bp;
    }
    if (prev_alloc && !next_alloc)
    {
        size += GET_SIZE(HDRP(NEXT_BLKP(bp)));
        PUT(HDRP(bp), PACK(size, 0));
        PUT(FTRP(bp), PACK(size, 0));
    }
    else if (!prev_alloc && next_alloc)
    {
        size += GET_SIZE(HDRP(PREV_BLKP(bp)));
        PUT(FTRP(bp), PACK(size, 0));
        PUT(HDRP(PREV_BLKP(bp)), PACK(size, 0));
        bp = PREV_BLKP(bp);
    }
    else
    {
        size += GET_SIZE(HDRP(PREV_BLKP(bp))) + GET_SIZE(HDRP(NEXT_BLKP(bp)));
        PUT(HDRP(PREV_BLKP(bp)), PACK(size, 0));
        PUT(FTRP(NEXT_BLKP(bp)), PACK(size, 0));
        bp = PREV_BLKP(bp);
    }
    return bp;
}

```